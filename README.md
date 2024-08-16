# INTERAKSI MANUSIA ROBOT BERBASIS LARGE LANGUAGE MODEL UNTUK PEMBANGKITAN ACTION PLAN PADA ROBOT LENGAN.

[![Product Name Screen Shot][product-screenshot]](https://example.com)

Repo ini akan membahas tugas akhir bagaimana kita dapat mengontrol robot lengan (Dobot Magician) dengan Input text bahasa alami yang diolah dengan LLM yang telah di finetune untuk menghasilkan Low Level JSON Action Plan yang akan dieksekusi robot secara real time

Ada dua bagian dari proyek ini:
* Finetuning base model LLM. Dalam proyek ini saya menggunakan model Gemma untuk LLM, Low Rank Adaptation (LoRA) untuk finetuning, dan Unsloth untuk library inferrence dan finetuning.
* Implementasi dengan robot. Singkatnya user akan memasukkan input text bahasa alami yang selanjutnya akan dikirim ke LLM dan akan menghasilkan Low Level JSON Action Plan. JSON ini akan dibaca dan dieksekusi satu persatu (misalkan if JSON.data == gerak, moveDobot() akan dipanggil).

Untuk keterangan lebih lanjut, buka README di folder [Finetuning](./finetuning) dan [Implementasi](.implementasi).

