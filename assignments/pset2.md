# Problem Set 2
Gloria Pulido

## Exercise 1

### 1. The NonceGeneration concept ensures that the short strings it generates will be unique and not result in conflicts. What are the contexts for, and what will a context end up being in the URL shortening app?

The contexts allow for consideration of the reasoning behind why a nonce is being generated. For example, generating a nonce for the need of getting a personal access token in Github versus needing one for a URL are two very different purposes and if there were overlap where something such as 'abcdefg' were both a token string and the suffix for a shortened URL, it would be acceptable. It would be in the case where if two 'abcdefg' tokens and two 'abcdefg' shortened URLs on TinyUrl were generated where problems would arise. The NonceGeneration concept has its purpose of generating short unique strings and has different contexts where even if two of the same were generated, no conflicts would arise. The contexts add that differentiation to ensure that within the set of Strings for a specific Context, they are unique and do not result in conflicts but there may be overlap between the set of Strings for another different Context. Within a URL shortening app, the Context would end up being the prefix in the URL. For example, using TinyUrl versus Bitly would be two different contexts. Though something such as tinyurl.com/abcdefg and bit.ly/abcdefg would have the same 'abcdefg' nonce generated, they have two different base URLs, which makes them different contexts and allows for them to have that overlap. Unless a user went ahead to make both these shortened URLs using the generated nonce to point them to the same URL, these two URLs would lead to different longer URLs. 

### 2. Why must the NonceGeneration store sets of used strings? One simple way to implement the NonceGeneration is to maintain a counter for each context and increment it every time the generate action is called. In this case, how is the set of used strings in the specification related to the counter in the implementation? (In abstract data type lingo, this is asking you to describe an abstraction function.)
### 3. One option for nonce generation is to use common dictionary words (in the style of yellkey.com, for example) resulting in more easily remembered shortenings. What is one advantage and one disadvantage of this scheme, both from the perspective of the user? How would you modify the NonceGeneration concept to realize this idea?


[ReadMe](../README.md)

