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
"instruction":"Summarize the given article in 200 Words.",
"input": "https://www.bbc.com/news/world-51461830",
"output": "The recent protests in Belarus are part of a growing wave of discontent against the rule of Alexander Lukashenko, who has led the country since 1994. Demonstrations against his government have been ongoing since August, and come in the wake of an election widely seen as rigged. As police and security forces have cracked down on protesters, the movement has only grown, with hundreds of thousands of people taking to the streets to call for new leadership. Human rights activists have accused the government of using extreme violence in order prosecute protesters, leading to an international outcry and sanctions from the European Union. As the situation in Belarus continues to unfold, the international community is monitoring the situation closely - hoping for a peaceful resolution, while also standing firm against the oppressive tactics employed by the Belarusian state."

"instruction": "Classify the following shape according to simple geometry.",
"input": "<Picture Attached>",
"output": "Triangle"

"instruction": "Given a recent news article, write a headline for it.",
"input": "https://www.news.example.com/article-1",
"output": "\"NASA Launches a Mars 2020 Mission to Search for Signs of Ancient Life\""
```

