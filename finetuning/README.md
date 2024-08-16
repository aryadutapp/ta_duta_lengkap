<!-- ABOUT THE PROJECT -->
# Finetuning

[![Product Name Screen Shot][product-screenshot]](https://example.com)

Finetuning adalah tindakan untuk menyesuaikan ulang base model LLM agar performanya meningkat untuk dataset domain spesifik tertentu. LLM dilatih dengan dataset pengetahuan umum (wikipedia, buku, dll) sehingga mempunyai pemahaman umum mengenai dunia di sekitarnya. Namun jika kita mempunyai keperluan khusus (misal LLM untuk chatbot medis atau untuk mengontrol robot), kita perlu melatih LLM dengan dataset yang lebih spesifik agar dapat menghasilkan output yang kita inginkan)

Berikut keperluan alat dan metode:
* Model LLM yang digunakan adalah [Gemma 2B](https://huggingface.co/google/gemma-2b)
* Metode finetuning yang digunakan adalah Low Rank Adaptation [LoRA](https://arxiv.org/abs/2106.09685)
* Library untuk inferrence dan finetuning adalah unsloth [Unsloth](https://github.com/unslothai/unsloth)

## Dataset

Sebelumnya kita harus menyiapkan dataset. Untuk format dataset saya menggunakan format [Alpaca](https://github.com/gururise/AlpacaDataCleaned). Format ini cocok karena terdiri dari instruction, input, dan output yang mengandung cukup informasi bagi LLM untuk membuat Action Plan. Untuk format instruction berisi mengenai instruksi agar LLM dapat membuat rencana aksi (misalkan fungsi yang dapat diakses, format yang diinginkan (JSON), dan batasan lingkugan (constrain). Input berisi perintah pengguna (misalkan geser lengan robot ke depan), dan Output berisi Action Plan yang diharapkan. Untuk Prompt Engineering masih menjadi topik hangat yang terus berkembang. Meskipun tidak ada satu metode one-size-fits-all dalam membuat prompt yang bagus, berikut rekomendasi untuk prompting LLM untuk robot [GPT for Robotics](https://www.microsoft.com/en-us/research/uploads/prod/2023/02/ChatGPT___Robotics.pdf) yang dapat dipertimbangkan . Untuk contoh dataset yang saya gunakan akan dicantumkan di bawah.

