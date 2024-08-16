<!-- ABOUT THE PROJECT -->
# Finetuning

Finetuning adalah tindakan untuk menyesuaikan ulang base model LLM agar performanya meningkat untuk dataset domain spesifik tertentu. LLM dilatih dengan dataset pengetahuan umum (wikipedia, buku, dll) sehingga mempunyai pemahaman umum mengenai dunia di sekitarnya. Namun jika kita mempunyai keperluan khusus (misal LLM untuk chatbot medis atau untuk mengontrol robot), kita perlu melatih LLM dengan dataset yang lebih spesifik agar dapat menghasilkan output yang kita inginkan)

Berikut keperluan alat dan metode:
* Model LLM yang digunakan adalah [Gemma 2B](https://huggingface.co/google/gemma-2b)
* Metode finetuning yang digunakan adalah Low Rank Adaptation [LoRA](https://arxiv.org/abs/2106.09685)
* Library untuk inferrence dan finetuning adalah unsloth [Unsloth](https://github.com/unslothai/unsloth)

## Dataset

Sebelumnya kita harus menyiapkan dataset. Untuk format dataset saya menggunakan format [Alpaca](https://github.com/gururise/AlpacaDataCleaned). Format ini cocok karena terdiri dari instruction, input, dan output yang mengandung cukup informasi bagi LLM untuk membuat Action Plan. Untuk format instruction berisi mengenai instruksi agar LLM dapat membuat rencana aksi (misalkan fungsi yang dapat diakses, format yang diinginkan (JSON), dan batasan lingkugan (constrain). Input berisi perintah pengguna (misalkan geser lengan robot ke depan), dan Output berisi Action Plan yang diharapkan. Untuk Prompt Engineering masih menjadi topik hangat yang terus berkembang. Meskipun tidak ada satu metode one-size-fits-all dalam membuat prompt yang bagus, berikut rekomendasi untuk prompting LLM untuk robot [GPT for Robotics](https://www.microsoft.com/en-us/research/uploads/prod/2023/02/ChatGPT___Robotics.pdf) yang dapat dipertimbangkan . Untuk contoh dataset yang digunakan akan dicantumkan di bawah.

```
"instruction":

"Objektif: Tugas anda adalah menghasilkan urutan respons JSON untuk merencanakan tindakan lengan robot berdasarkan input pengguna. Jika tujuan tidak dapat dicapai dengan menggunakan instruksi yang disediakan dan objek yang tersedia, kembalikan pesan kesalahan.

Berikan objek JSON yang mengandung array "actions", diidentifikasi dengan key "actions".

Setiap aksi harus direpresentasikan sebagai objek dengan "command" dan "parameters" yang sesuai

Objek dan Koordinat yang Tersedia (x,y,z):
1. Balok ungu = (-86.59, 117.21, -122.30)
2. Balok kuning = (-168.94, -129.37, -68)
3. Balok biru = (152.76, 158.92, 6)

Perintah yang Tersedia:
1. move: Gerakkan lengan robot ke arah tertentu. Sertakan parameter "direction" dengan nilai "atas", "bawah", "depan", "belakang", "kiri", atau "kanan".
2. move_to: Gerakkan lengan robot ke koordinat tertentu. Sertakan parameter "x", "y", dan "z" untuk menentukan koordinat tujuan.
3. suction_cup: Aktifkan atau nonaktifkan cup hisap. Gunakan parameter "action" dengan nilai "on" atau "off".
5. err_msg: Kembalikan pesan kesalahan jika tujuan pengguna tidak dapat tercapai dengan menggunakan objek dan perintah saat ini. Gunakan parameter "msg" dengan nilai "tidak dapat membuat rencana aksi dengan kondisi terkini".

Contoh Penggunaan Perintah:
"{"actions":[{"command":"move","parameters":{"direction":"atas"}},{"command":"move_to","parameters":{"x":-30.21,"y":233.32,"z":-40}},{"command":"suction_cup","parameters":{"action":"on"}},{"command":"err_msg","parameters":{"msg":"tidak dapat membuat rencana aksi dengan kondisi terkini"}}]}"

Instruksi Penggunaan:
1. Untuk memindahkan objek yang tersedia ke koordinat tertentu, aktifkan penyedot terlebih dahulu menggunakan perintah "suction_cup" dengan "action" diatur ke "on", kemudian gerakkan ke koordinat objek menggunakan perintah "move_to".
2. Berikan koordinat penempatan untuk tujuan pengguna menggunakan perintah "move_to".
3. Untuk melepaskan objek setelah menggunakan penyedot, nonaktifkan penyedot terlebih dahulu menggunakan perintah "suction_cup" dengan "action" diatur ke "off".
4. Untuk memindahkan robot secara lateral (misalnya ke kiri, kanan, depan, belakang, atas, depan), gunakan perintah "move" dengan arah yang sesuai.
5. Untuk memindahkan objek secara lateral ((misalnya ke kiri, kanan, depan, belakang, atas, depan), pertama-tama gerakkan lengan robot ke koordinat objek menggunakan perintah "move_to", kemudian gunakan perintah "move" dengan arah yang sesuai.
6. Jika tujuan pengguna tidak dapat tercapai dengan perintah dan objek saat ini, gunakan perintah "err_msg".",

"input": "pindahkan posisi balok biru ke posisi kiri",

"output": "{"actions": [{"command": "move_to", "parameters": {"x": 152.76, "y": 158.92, "z": 6}}, {"command": "suction_cup", "parameters": {"action": "on"}}, {"command": "move", "parameters": {"direction": "kiri"}}, {"command": "suction_cup", "parameters": {"action": "off"}}]}"

```

Berikut hal yang perlu diperhatikan:
* Dalam implementasi dunia nyata, Objek dan Koordinat yang Tersedia akan diinject secara real time dengan object detection. Namun untuk keperluan finetuning, akan dibuat tetap. Jika ingin membuat objek yang berbeda dapat ditambahkan langsung. Pastikan untuk koordinat objek variatif (tidak integer semua atau bilangan positif semua), karena sepengalaman jika koordinat tidak variatif maka output LLM yang dihasilkan juga tidak sesuai (misalkan koordinat -210.32 akan menjadi 210 saat LLM menghasilkan action plan karena koordinat kurang variatif.
* Pastikan untuk membuat prompt yang mengandung seluruh informasi yang diperlukan. Ingat LLM tidak memahami kondisi lingkungan nyata dan pastikan prompt dapat menyediakan seluruh informasi yang diperlukan. Mungkin perlu dicoba beberapa kali sampai menghasilkan output yang sesuai. Jika menggunakan model lain (selain Gemma), jangan lupa membaca prompting / finetuning guide dari model tersebut.
* Buat berbagai macam skenario input untuk meningkatkan performa model

Untuk dataset lengkap dapat diakses di 🤗 (https://huggingface.co/datasets/Aryaduta/test-data2)

## Finetuning

Setelah menyiapkan dataset kita dapat melakukan finetuning. Untuk metode finetuning yang digunakan adalah Low Rank Adaptation dengan menggunakan Library Unsloth. Perlu diperhatikan untuk finetuning pastikan mempunyai VRAM yang cukup (pada percobaan di google colab (free gpu), 14 gb vram lebih dari cukup untuk inferrence dan finetuning). Untuk unsloth pada saat penulisan ini hanya tersedia di Linux (Windows bisa dengan WSL). 

