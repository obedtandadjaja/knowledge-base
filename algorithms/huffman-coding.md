# Hufman Coding

Hufman coding is a lossless data compression (unlike video/music compression where some loss is acceptable).

The idea is to have high frequency characters to be encoded in smaller length (less than 8 bits (256) - ASCII standard) than less frequent characters. The reason why the characters can be encoded in differing lengths is because each encoding does not intersect with other encoding's prefix.

Example:
Let A = 010, Let E = 000, Let F = 1101

```
0101101010010000
A  F   A  A  E
```

Notice how the above encoding cannot be translated to other results since each character's encoding does not intersect with another's encoding prefix.

The way this encoding is created is by constructing a frequency binary tree with characters on its leaves. Each path to the left = 0 and each path to the right = 1. Since all the characters are on the leaves, the path for each character is unique and thus the encoding must be unique as well.

See below on how you can construct a huffman code tree:
1. Count frequency of each character
2. Sort the frequency in descending order
3. Create binary tree of the two least frequent nodes with the sum of the character's frequency as its root. Add this binary tree back to the list
4. Keep repeating step 3 until all elements for a binary tree

![image](https://upload.wikimedia.org/wikipedia/commons/d/d8/HuffmanCodeAlg.png)

Note that this encoding requires the mapping tree to be included in the compression and so it might take more space when the thing it is encoding is small in size. As a result, Hufman Coding should be used on larger texts or larger files.

Resources:
- https://en.wikipedia.org/wiki/Huffman_coding
- https://www.youtube.com/watch?v=JsTptu56GM8&ab_channel=TomScott
