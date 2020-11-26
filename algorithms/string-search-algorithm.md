# String search algorithms

## Boyer-Meyer

Wikipedia: https://en.wikipedia.org/wiki/Boyer-Moore_string_search_algorithm

Golang version: https://golang.org/src/strings/search.go

Goal:

```
Input:  txt[] =  "AABAACAADAABAABA"
        pat[] =  "AABA"
Output: Pattern found at index 0
        Pattern found at index 9
        Pattern found at index 12
```

- Boyer-Meyer preprocesses the pattern and does not preprocess the input.
- A combination of following two approaches.
  - Bad Character Heuristic
  - Good Suffix Heuristic
- Matches pattern from right to left while iterating through the input from left to right. This enables the pattern to skip characters through heuristic.
- Processes the pattern and creates different arrays for both heuristics. At every step, it slides the pattern by the max of the slides suggested by the two heuristics. So it uses best of the two heuristics at every step.

### Bad character heuristic

Case 1: character exists in pattern, skip to the character in the pattern

![image](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/boyer-moore1.jpg?raw=true)

Case 2: character does not exist in pattern, skip by the length of the pattern

![image](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/boyer-moore2.jpg?raw=true)

### Good suffix heuristic

Case 1: Suffix exists in pattern

![image](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/boyer-moore3.jpg?raw=true)

Case 2: Partial suffix exists in pattern

![image](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/boyer-moore4.jpg?raw=true)

Resources:
- https://www.coursera.org/lecture/algorithms-part2/boyer-moore-CYxOT
- https://www.youtube.com/watch?v=lkL6RkQvpMM&ab_channel=KjHuang
