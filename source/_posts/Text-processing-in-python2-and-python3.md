---
title: Text processing in python2 and python3
date: 2018-10-31 15:41:58
tags:
mathjax: true
---
One big difference between python2 and python3 is about strings. I have been bitten by this serval times. The motivation of this blog is clearify some basic questions.

### Code Point
We all know computers can only understand 0s and 1s, to represent a character or a string, we need such kind binary representation, a well-known code is 
[`ASCII`](https://www.cs.cmu.edu/~pattis/15-1XX/common/handouts/ascii.html), for example:

```text
a = 01100001
b = 01100010
c = 01100011
```

The problem with `ASCII`'s 8 digit is that there are only 256 combinations, of course there are some other encodings, but that is beyond this blog.

In a nutshell, a code point is an integer, represents a character.

### Unicode code point

The difference between `Code Point` and `Unicode code point` is, Unicode code points are usually noted as hexadecimal with a `U+` in the very begining, it is the convention. Then the `Unicode code point` for letter a, b and c are:

```text
a = U+0061
b = U+0062
c = U+0063
```

### Text vs Bytes
When talking about the strings, we usually mean the text you see on the screen as this blog, let's call it `human text`, and the way a machine can understand is binary data, so `bytes`/`str` is employed in Python3/Python2 to represent the `machine text`, `binary data`.

Following is an example of the text `café`

```python
# Python3 version
s = 'café'
print(s)
#café
encoded_cafe = s.encode("utf8")
print(encoded_cafe)
#b'caf\xc3\xa9'
decoded_cafe = encoded_cafe.decode("utf8")
print(decoded_cafe)
#café
```

If you copy the code above in Python2, you will see:

```python
s = 'café'
print(s)
#café
encoded_cafe = s.encode("utf8")
#Traceback (most recent call last):
#  File "<stdin>", line 2, in <module>
#UnicodeDecodeError: 'ascii' codec can't decode byte 0xc3 in position 3: ordinal not in range(128)
```

 We will dive into this error later and find a solution for it.

### Encode vs Decode
I was confused about the encode and decode step until I find a good analogy of it. 
1. Encode / Encrypt
Consider the encode step is a encryption, we are translate a `human text` to `machine text` or `bytes`, which can be only understood by machines, that is `café` -> `caf\xc3\xa9`, the first three characters are still represnted by themselves for convenience, but the `é` is reprensted as two bytes.
Without knowing the encoding method, `caf\xc3\xa9` can be interpreted as a different string:

```python
b'caf\xc3\xa9'.decode("gb2312")
#'caf茅'
b'caf\xc3\xa9'.decode("latin1")
#'cafÃ©'
```

The code above explains the analogy between `encode` and `encrypt`.

2. Decdoe / Decrypt
Given the the context of encode, we should naturally think about the analogy between `decode` and `decrypt`. To correctly "decrypt" the `bytes`, we need to know the encoding charset. 

### str vs unicode vs bytes vs bytearray
After talking about the encode and decode, let's take a look about the types for strings.
There are two built-in types in both Python2 and Python3:

$$
\textrm{Python2}=\begin{cases}
           \textrm{str} \rightarrow \textrm{raw bytes}\\
           \textrm{unicode} \rightarrow \textrm{human text}
        \end{cases}
$$

$$
\textrm{Python3}=\begin{cases}
           \textrm{str} \rightarrow \textrm{human text}\\
           \textrm{unicode} \rightarrow \textrm{raw bytes}
        \end{cases}
$$

### Problem Solving
Let's answer why the very first code snippet only works in Python3, and how to write the Python2 code.
* Analysis
In Python2, when you define a string with `r''` or `single quote` or `double quotes` or `triple quotes`, it means `str` in Python2, which is the machine text, binary data, whereas in Python3, a string defined  with `r''` or `single quote` or `double quotes` or `triple quotes` also means `str` in Python3, but it is HUMAN TEXT, so you can encode it.
* Solution
Add `from __future__ import unicode_literals`, this will make variable `s` a `unicode` in Python2 when you creating it, which is equivalent to `str` in Python3. Refer [this link about the pros and cons of this statement](https://python-future.org/unicode_literals.html)

We will save the `bytes` and `bytesarray` in the Compatibility section

### Compatbility
1. bytes in Python2
  In python2, `bytes` is just an alias for `str`, you can simply check the [source code](https://github.com/python/cpython/blob/2.7/Python/bltinmodule.c#L2725) here and [source_code](https://github.com/python/cpython/blob/2.7/Python/bltinmodule.c#L2755) here, or search `&PyString_Type` to confirm it.
2. bytearray in Python2  
  The `bytearray` was imported to python2 after people make `bytes` immutable([PEP3137](https://www.python.org/dev/peps/pep-3137/)), it is just an array of bytes, the only caveat is:
    - The slice of `bytearray` is still `bytearray`
    - The indexing of `bytearray` is integer
    - All python array's indexing except bytearray will return the type of element itself.
3. use `from __future__ import unicode_literals` in python2 code, [pros and cons](https://python-future.org/unicode_literals.html), so that you will get `unicode` type in Python2 when you create strings.

### best practice
1. use `from __future__ import unicode_literals` wisely, [pros and cons](https://python-future.org/unicode_literals.html)
2. always specify encoding, and "utf8" is strongly recommended.
3. when reading text, always decode it to `human text`, then do whatever processing you like, and translate it to `bytes` to persist the data.

### bonus knowledge
1. if you are given a sequence of bytes without the encoding, you can `guess` the encoding with [`chardet`](https://pypi.org/project/chardet/)

### References:
1. https://pythonhosted.org/kitchen/unicode-frustrations.html
2. https://docs.python.org/3/howto/unicode.html
3. https://docs.python.org/2/howto/unicode.html
4. https://www.pythonsheets.com/notes/python-unicode.html
