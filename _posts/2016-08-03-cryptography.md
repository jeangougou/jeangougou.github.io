---
layout: post
title:  "Why cryptography?"
date:   2016-08-03 18:05:23 +0000
categories: programming
description: "what is an how we use cryptography"
published: true
tags:
- cryptography
---

### What is cryptography ?

If we go back to the root of the word itself [crypto][crypto]([or in ancient Greek][crypto-Greek]), meaning secret, hidden or invisible, and the suffix [-graphy][graphy]([or in ancient Greek][graphy-Greek]). You probably knew that already.

The most common one sentence definition found in the wild are secret writing, or the process of writing or reading secret messages or codes.
If we want to be really precise, we can always refer to what I personally believe being the best online dictionary, and follow [its definition of cryptography][mw-definition-crypography]:
> the enciphering and deciphering of messages in secret code or cipher; also :  the computerized encoding and decoding of information

### Why use it ?

Cryptography is useful as it can help sharing information in secrecy. Secrecy is intended here in regards to personal privacy or company secrets, not criminal activities. The art has been extended massively in the last few decades, particularly after the second world was and it is now used also to validate that communication is happening among trusted clients. If you have heard of bitcoins and its infamous block-chain I can assure you all that under the hood it leverages heavily cryptography.

### How does it work ?

In an extreme attempt of simplification we can decompose the process of transforming a plain text into a cypher text into two operations: transpositions, substitutions. But it's only mainly the first two.

Transpositions happens when we rearrange the order of the content

```
Happy days!
```

can become:

```
!syad yppaH
```

This simple transposition, done through a straightforward JS command (```"Happy days!".split('').reverse().join('')```), is obtained by writing the full text in the reverse order. This is possibly the simplest transposition achievable, any other order, as long as it is repeatable will work fine.

Substitution happens when we replace a block of content with a different one. This operation can be further divided into subcategories:
- Nullables, when only adding blocks that will have to be discarded in the deciphering phase
- Omophones, applies only to natural languages, when substituting blocks with similarly sounding blocks (when pronounced or different languages)
- Variable size, when the size of the block in the plain text is different from the size of the block in the encrypted text
- Same size, when the plain text has the same length of its encrypted counterpart

### Can I use it ?

You are already using if without realizing. Have you seen the padlock next to your address bar let's say on your bank website ?
That's using cryptography to try and establish the authentication of the service provider.
You should use it, in fact, particularly for your email or any sensitive data you want to protect.
You can even implement your own version of the Caesar cypher if you want to. This will not be very safe choice, it's quite easy to break.

### Can the government read my encrypted text ?

Yes. Absolutely, there's no way they can't. They are even trying to prevent any citizen from using cryptography properly with laws that go way beyond the need to establish and enforce security (see [IPBill][IPBill]).
A brief history lesson will tell you all about DES implementation that the NSA suggested IBM to use. Just Google DES NSA Backdoor and you'll find out.
History tends to repeat itself therefore it is logical to presume that entire countries has the resources and the power to break your cryptography. Realistically, no one is really interested in your secrets chat that much.

[crypto](https://en.wiktionary.org/wiki/crypto-#English)
[crypto-Greek](https://en.wiktionary.org/wiki/%CE%BA%CF%81%CF%85%CF%80%CF%84%CF%8C%CF%82#Ancient_Greek)
[graphy](https://en.wiktionary.org/wiki/-graphy#English)
[graphy-Greek](https://en.wiktionary.org/wiki/%CE%B3%CF%81%CE%AC%CF%86%CF%89#Ancient_Greek)
[mw-definition-crypography](http://www.merriam-webster.com/dictionary/cryptography)
[IPBill](https://en.wikipedia.org/wiki/Investigatory_Powers_Bill)
