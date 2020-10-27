---
layout: default
title: Hashcat
parent: CHEATSHEETS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[Hashcat Wiki](https://hashcat.net/wiki/)

[Example hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)

[General use](https://miloserdov.org/?p=953)

[Using Hashcat rules](https://hashcat.net/forum/thread-7195.html)

## Getting the hash mode value
Either check the [Hashcat wiki example hashes page](https://hashcat.net/wiki/doku.php?id=example_hashes), search the `man` page or `grep` the help output.

## Brute forcing hashes examples
```csharp
// NTLM
hashcat -m 1000 file-containing-hashes /usr/share/wordlists/rockyou.txt

// Applying rules files to the wordlist
hashcat -m 1000 file-containing-hashes /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

## Hashcat.potfile
Cracked hashes are added to `hashcat.potfile` which is in the same directory as the binary. Hashcat checks this file first and outputs the hash and result if it's found, with the message:

```
INFO: All hashes found in potfile! Use --show to display them.
```

You can then show the password using
```
hashcat --show <hash you are trying to crack>
```

The `.potfile` is a text document so you can use `cat` and `grep` to seearch it. To clear all previously cracked hashes, delete the file.

## Generating Rules-Based Wordlists

*Only use small wordlists or the resulting files will be huge! For large wordlists it's quicker to use `-r` in hashcat.*

Rules files are located in `/usr/share/hashcat/rules/`.

```
// Example to see how many items are generated per word.
echo password | hashcat -r /usr/share/hashcat/rules/best64.rule --stdout

// For wordlist.
cat /usr/share/wordlists/my-password-wordlist | hashcat -r /usr/share/hashcat/rules/best64.rule --stdout > my-password-wordlist-hashcat-best64

// Using character sets (from above reference).
hashcat -m 0 -a 3 -1 'hasct' --stdout ?1?1?1?1?1?1?1

sattasa
chatasa
ashaasa
tattasa
...

// Starting with the word 'hash'.
hashcat -m 0 -a 3 -1 'cat' --stdout hash?1?1?1

hashata
hashtat
hashcat
hashaca
...
```
