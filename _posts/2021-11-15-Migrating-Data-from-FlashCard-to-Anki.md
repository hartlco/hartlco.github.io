---
layout: post
title: "Migrating data from FlashCard.app to Anki"
date: "2021-11-15 05:00"
---

Early on while learning Japanese, I started looking for flashcard Apps to memorize Hiragana, Katakana, and basic vocabulary. Because I am me, I wanted a simple and native iOS App for it. I landed on [FlashCard.app](http://flashcard.mobi) and started adding many flashcards. After a while, I got frustrated with the lack of repeating memorization strategies and started using [Anki.app](https://apps.ankiweb.net).

[FlashCard.app](http://flashcard.mobi) does not offer a real export feature, not interested in adding hundreds of flashcards again, I started digging with [Proxyman](https://proxyman.io) on iOS. 

**Not a tutorial, just sharing the basic idea:**

When FlashCard.app downloads new flashcards sets with the `Discover` feature, the data is transmitted using a `.tsv` response body. By sharing your flashcards using the classroom feature, you can download your cards again on a different device. Using Proxyman, you can then fish out your own sets as a `.tsv` data. After removing the meta-data lines, you can then import this data into Anki by configuring your fields to match with the data inside the `.tsv`.