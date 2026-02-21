# Weavish
A graph-birelational constructed language

## Introduction
| Terminology | Analogy in other Languages | Remarks
|-|-|-|
| Word / Node / Vertex| Word| A unique symbol enbodying a single concept. Digitally written as English words in all-caps. 
| Relation / edge | Attention (GPT) | A line drawn between two nodes, representing the relationship between two words in a sentence. Each relation has exactly two roles, indicating the grammatical role the word serves in a sentence. 
| Sentence / Web | Sentence | A connected network of multiple nodes. Represents a single sentence. 
| Graph | Paragraph | A collection of webs. Represents a paragraph. 
| Role / Anchor | Grammatical Roles | An anchor drawn at the end of a edge to point at a node. 
| Object (Role) | Noun | Drawn as a circle at the end of a edge. Digitally written as `--()`
| Method (Role) | Verb | Drawn as a diamond at the end of a edge. Digitally written as `--<>`
| Property (Role) | Modifier | Drawn as a square at the end of a edge. Digitally written as `--[]`

**Example 1** 
Consider the following sentence. 
```
I ()--<> EAT <>--(1) APPLE
```
There are three nodes
- `I`
- `EAT`
- `APPLE`

And two relations
- `I ()--<> EAT`
- `APPLE (1)--<> EAT`

In the sentence above. Note that `APPLE (1)--<> EAT` is considered equivalent to `EAT <>--(1) APPLE`, because the direction of a relation does not matter. 

In the first relation `I ()--<> EAT`, `I` is the object and `EAT` is the method. If the reader is familiar with programming languages, they can infer that it is analogous to referring to a method of an object, i.e. `I.EAT`. 

In the second relation `APPLE (1)--<> EAT`, the role `--(1) APPLE` means that `APPLE` is the direct subject of the verb `EAT`. (In Weavish jargon, `APPLE` is the first argument of the method `EAT`) Note that there is a 0-th argument of `EAT`, which is `I`, as the relation can be also written as `I (0)--<> EAT`. If the reader is familiar with Python, they can consider the following code that always outputs `True`. 

```
Class Word:
    def EAT(self, direct_subject):
        ...

I = Word(...)
APPLE = Word(...)
print(
    I.EAT(APPLE) == Word.EAT(I, APPLE)
)
```

**Identifaction Number (ID)** In Weavish, you can assign an identification bumber (ID) to a node, so that you can point multiple relations to the same node. If the two relations in **Example 1**
- `I ()--<> EAT`
- `APPLE (1)--<> EAT`

Are put together as 

```
I ()--<> EAT
APPLE (1)--<> EAT
```

This would not be one sentence but two sentences, because each time you write a word without referencing to a existing word, you are declaring a new instance of the word. As the result, the two `EAT`s are considered diferent instances, and the sentences are translated to 

> I eat. \
Apple is eaten.

To refer to an existing insance of word, one has to assign an ID to the word using the commutative `=` operator. IDs must be natural numbers. 

```
I ()--<> EAT = 1 
I ()--<> 1 = EAT 
# Because "=" is commutative, 
# you can assign "1" to "EAT" in both ways!
```

To refer to the ID-ed word, one can simply use the dollar sign marker `#n` or repeat the assignment `WORD = n`, i.e. 

```
APPLE (1)--<> #1
APPLE (1)--<> EAT = 1
APPLE (1)--<> 1 = EAT
# They are all valid expressions!
```

Therefore, to translate "I eat apple" to Weasish, one can write 

```
I     ( )--<> EAT = 0
APPLE (1)--<> EAT = 0
```
Which is the recommanded way to write in Weavish. 

## Matrix Representation 
We assign priorities to each role except the subjects

|Name | Literal | Priority
|-|-|-|
| Object| `()` | 0
| Method| `<>` | 1
| Property | `[]` | 2
| Subject | `(n)` | -

So that each relation has at least one direction (or two directions if the relation has two same roles), pointing from the role with higher priority to the role lower priority. For example, the relation 
`I ()--<> EAT = a` points from `I` to `EAT`, because `()` is higher prioritized than `<>`. (Smaller the number indicates higher priotity) We can sort all of the unique relations by comparing their higher priority roles (if they are the same, compare the other roels). 

| Index | Name | Relation | Priorities | 
|-|-|-|-|
|1| Objective Identity Relation | `()--()` | 0-0
|2| Invocation Relation| `()--<>` | 0-1
|3| Accession Relation| `()--[]` | 0-2
|4| Functional Identity Relation| `<>--<>` | 1-1
|5| Functional Accession Relation | `<>--[]` | 1-2
|6| Characteristic Identity Relation| `[]--[]` | 2-2
|6+n| Argumentation Relation | `<>--(n)` | 1-2

We can then use matraces to encode sentences. Recall **Example 1**

```
I ()--<> EAT <>--(1) APPLE
```

First, we assign each word an ID consectively. 

```
I=0 ()--<> EAT=1 <>--(1) APPLE=2
```

Consider the python code. 

```
nodes = ['I', 'EAT', 'APPLE']

def id(node):
    for (i,nd) in enumerate(nodes)
        if nd == node:
            return i
    raise Exception(f'The word {node} does not have an ID')

sentence = [
    [0, 2, 0],
    [0, 7, 0],
    [0, 0, 0],
]

# print the relation between 
print(
    sentence[id("I")][id("EAT")]
)
# This will outpu "1", which is the index of the invocation relation pointing from "I" to "EAT", indicating the roles of each node being "()" and "<>". 
```