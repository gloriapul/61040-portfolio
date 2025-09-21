# Problem Set 2
Gloria Pulido

[ReadMe](../README.md)

## Exercise 1

### 1. The NonceGeneration concept ensures that the short strings it generates will be unique and not result in conflicts. What are the contexts for, and what will a context end up being in the URL shortening app?

The contexts allow for consideration of the reasoning behind why a nonce is being generated. For example, generating a nonce for the need of getting a personal access token in Github versus needing one for a URL are two very different purposes and if there were overlap where something such as 'abcdefg' were both a token string and the suffix for a shortened URL, it would be acceptable. It would be in the case where if two 'abcdefg' tokens and two 'abcdefg' shortened URLs on TinyUrl were generated where problems would arise. The NonceGeneration concept has its purpose of generating short unique strings and has different contexts where even if two of the same were generated, no conflicts would arise. The contexts add that differentiation to ensure that within the set of Strings for a specific Context, they are unique and do not result in conflicts but there may be overlap between the set of Strings for another different Context. Within a URL shortening app, the Context would end up being the prefix in the URL. For example, using TinyUrl versus Bitly would be two different contexts. Though something such as tinyurl.com/abcdefg and bit.ly/abcdefg would have the same 'abcdefg' nonce generated, they have two different base URLs, which makes them different contexts and allows for them to have that overlap. Unless a user went ahead to make both these shortened URLs using the generated nonce to point them to the same URL, these two URLs would lead to different longer URLs. 

### 2. Why must the NonceGeneration store sets of used strings? One simple way to implement the NonceGeneration is to maintain a counter for each context and increment it every time the generate action is called. In this case, how is the set of used strings in the specification related to the counter in the implementation? (In abstract data type lingo, this is asking you to describe an abstraction function.)

NonceGeneration must store the sets of used strings as that is how within a context, it keeps track of what strings have already been generated. This ensures that no string is generated twice which the generate action checks for. It would ruin the purpose of the NonceGeneration concept as it is designed to generate unique strings and would be meaningless if the set of used strings was removed. 

When it comes to the implementation, it would be the case that the counter would essentially replace the set of used strings. By having a counter that is at, for example, 10, it would be known that strings representing 0 - 10 were already generated. The generate action may use some type of encoding such as base62 that maps each number to its corresponding string. This would mean that every string could be known based on the counter without having to actually store each one in a set of strings. The counter would only be allowed to increase after each string is generated, which ensures none are repeated.


### 3. One option for nonce generation is to use common dictionary words (in the style of yellkey.com, for example) resulting in more easily remembered shortenings. What is one advantage and one disadvantage of this scheme, both from the perspective of the user? How would you modify the NonceGeneration concept to realize this idea?

From the user's perspective, one advantage would be that those kinds of nonces could be much more easily remembered and could mean faster typing to get to their URL compared to having to type in purely random complicated phrases. A disadvantage for a user would be that getting generated nonces that make sense would become very difficult. There's a finite number of words and finding combinations that have not been generated yet would mean much longer generated phrases, which outweighs the chance it can be easily remembered and would instead mean more typing. It would defeat the purpose of using a URL shortener in that way as well. 

To modify the NonceGeneration concept, one would:

- Add a new state -> a dictionary Strings
- Add to the generate actions ->

> generate (context: Context) : (nonce: String)
>> require the word/phrase is not in the used set of Strings and the word(s) are in the dictionary\
>> effect returns a nonce that is not already used by this context

## Exercise 2

### 1. In the first sync (called generate), the Request.shortenUrl action in the when clause includes the shortUrlBase argument but not the targetUrl argument. In the second sync (called register) both appear. Why is this?

This is due to the fact that the generate is just for generating alone. Even if the targetUrl is not provided or taken in yet, it should still be able to generate a nonce for a shortenUrl. The register is what takes care of creating the mapping between the shortened url and the target url. A shortened url cannot be registered if the url it is meant to act as an alias for is not provided. Overall, this helps with creating a proper separation between the syncs and breaks down each step by having generate, register, and setExpiry. 

### 2. The convention that allows names to be omitted when argument or result names are the same as their variable names is convenient and allows for a more succinct specification. Why isn’t this convention used in every case?

Though it can be more succinct, it can increase confusion in certain cases, which is why the convention is not always used. For example, it can be seen that with setExpiry, it has both when ... shortUrl and then ... resource: shortUrl. In the first case, it was convenient to just have shortUrl as that is the result being received from the register sync. Meanwhile, the third line uses resource: shortUrl. resource is used as it provides more clarity as to what setExpiry sets out to do. It emphasizes what role shortUrl has, which is to act as a resource. It also highlights the fact that setExpiry is general and can have a variety of types of resources and in the case of the URL shortening, takes in the shortUrl from register. So, the convention is not always used as it can take away the opportunity, like if setExpiry hadn't specified resource, to make the sync or concept more broad and clear. 

### 3. Why is the request action included in the first two syncs but not the third one?

The request is included in the first two syncs since they are a direct result of a user making a request. A user can choose to have a nonce generated and will choose to have that nonce registered in order to get their shortened url. On the other hand, the user themself cannot request to set an expiration date. That is handled on the application's end as it automatically sets the expiration to be in 3,600 seconds. It would be bad practice to let the user request's trigger a sync that they do not have any control over. It only makes sense for the setExpiry to include register as that it means the user's request has gone through and now can have an expiration set on its newly created url. 

### 4. Suppose the application did not support alternative domain names, and always used a fixed one such as “bit.ly.” How would you change the synchronizations to implement this?

If it were just a fixed a fixed one like “bit.ly”, it would simplify the synchronizations. The user would no longer have to provide a shortUrlBase and it would default to “bit.ly.”

> sync generate
>> when Request.shortenUrl ()\
>> then NonceGeneration.generate (context: “bit.ly”)

> sync register
>> when\
>> Request.shortenUrl (targetUrl)\
>> NonceGeneration.generate (): (nonce)\
>> then UrlShortening.register (shortUrlSuffix: nonce, shortUrlBase: “bit.ly”, targetUrl)

> sync setExpiry
>> when UrlShortening.register (): (shortUrl)\
>> then ExpiringResource.setExpiry (resource: shortUrl, seconds: 3600)

### 5. These synchronizations are not complete; in particular, they don’t do anything when a resource expires. Write a sync for this case, using appropriate actions from the ExpiringResource and URLShortening concepts.
