# this_is_canon
`jellyctf{jelly_your_homework_was_due_yesterday}`
## Synopsis
A challenge that requires decoding a Canonical Huffman Encoded message. The flag is encoded using Canonical Huffman Encoding.
## Challenge Text
Is this meme still relevant? 
![canon_meme](/assets/canon_meme.jpg)
Note: Flag format for this challenge is all lowercase: jellyctf{lowercase_letters}

Author: Sheepiroo
## Attachments
flag.txt
```
(0,0,4,3,7,6)
(_,e,l,y,j,o,r,a,c,d,s,t,u,w,f,h,k,m,{,})
1000001010010011101111101011101011111010000010100100110000111001110111010000111011100111110100111100100110101111000001110010110110010001100011011001000011001110011101000110101100010110011111111
```

## Breakdown
The most difficult part of this challenge is to figure out exactly how the message was encoded. While at first glance it may seem like a simple Huffman encoding, we are not provided a dictionary of the symbols used in the encoding, nor the frequency of each symbol to work backwards. However, we are given a hint in the challenge title: "this_is_canon". Further, we notice that there are 20 symbols in the encoding, and the sum of the array in the first line is also 20. With some Googling, we find that the encoding is a Canonical Huffman Encoding.

## Attack
[Resource/Algorithm](https://pineight.com/mw/page/Canonical_Huffman_code.xhtml)

After some Googling, we find that a Canonical Huffman Encoding is a Huffman encoding format that is uniquely decodable without the need for a frequency table, as the second array is sequential. We can use the algorithm provided in the link to decode the message. 

```python
lt = [0,0,4,3,7,6]
codes = ['_', 'e', 'l', 'y', 'j', 'o', 'r', 'a', 'c', 'd', 's', 't', 'u', 'w', 'f', 'h', 'k', 'm', '{', '}']
bitstream = "1000001010010011101111101011101011111010000010100100110000111001110111010000111011100111110100111100100110101111000001110010110110010001100011011001000011001110011101000110101100010110011111111"

def canonicalhuffmandecode(lt, codes, bitstream):
    first_index_on_row = 0
    accum = 0
    row = 0
    decoded = ""
    for i in range(len(bitstream)):
        accum = accum * 2 + int(bitstream[i])
        if accum < lt[row]:
            decoded += codes[accum + first_index_on_row]
            accum = 0
            first_index_on_row = 0
            row = 0
        else:
            first_index_on_row += lt[row]
            accum -= lt[row]
            row += 1
    return decoded

print(canonicalhuffmandecode(lt, codes, bitstream))
```
Running the script, we get the flag `jellyctf{jelly_your_homework_was_due_yesterday}`
