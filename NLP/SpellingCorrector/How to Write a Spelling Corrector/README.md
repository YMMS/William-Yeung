## Reading Notes: [How to Write a Spelling Corrector](http://norvig.com/spell-correct.html)

The full details of an industrial-strength spell corrector are quite complex (more information can be found [here]() and [here]())

The author of this blog write and explain a toy spelling corrector that achieves 80 or 90% accuracy at a processing speed of at least 10 words per second in about half a page of code. The code is as beblow:

<pre><code>

import re
from collections import Counter


def words(text): 
    return re.findall(r'\w+', text.lower())

WORDS = Counter(words(open('big.txt').read()))


def P(word, N=sum(WORDS.values())): 
    "word词的概率"
    return WORDS[word] / N

def correction(word): 
    "最大概率的正确拼写词."
    return max(candidates(word), key=P)

def candidates(word): 
    "产生可能的正确拼写词"
    return (known([word]) or known(edits1(word)) or known(edits2(word)) or [word])

def known(words): 
    "在WORDS词典中出现的words子集."
    return set(w for w in words if w in WORDS)

def edits1(word):
    "构造word编辑距离为1的候选"
    letters    = 'abcdefghijklmnopqrstuvwxyz' # 字母集合
    splits     = [(word[:i], word[i:])    for i in range(len(word) + 1)] # 单词切断（切分成两部分）
    deletes    = [L + R[1:]               for L, R in splits if R] # 模拟单词漏掉一个字母
    transposes = [L + R[1] + R[0] + R[2:] for L, R in splits if len(R)>1] # 模拟字母写反
    replaces   = [L + c + R[1:]           for L, R in splits if R for c in letters] # 模拟字母被替换
    inserts    = [L + c + R               for L, R in splits for c in letters] # 模拟插入多余的字母
    return set(deletes + transposes + replaces + inserts) # 构造可能的候选

def edits2(word): 
    "构造word编辑距离为2的候选"
    return (e2 for e1 in edits1(word) for e2 in edits1(e1))

</code></pre>

