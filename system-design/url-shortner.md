Simplest way to create url shortener is to use a `counter`. Start with `counter=1` and increment this value on each time user submits a new url.

## Pros with this approach

* easy to implement

## Cons with this approach

* predictive (means it can be scraped)
* generated url is not very short

## Making url short

We can shorten the url using `base64`. For example, suppose, we have decimal no. `1_000_000_000`, this number can be represented in 7 bits.

```bash
Number of bits (n) = floor(log(number), base) + 1
=> floor(log(1_000_000_000), 64) + 1 
=> 7 bits
```

We can make short url less predictable using hashing.






