---
layout: post
category: ""
title:  "boost::tokenizer vs boost::split"
tags: [boost]
---

**This article is a reprint of someone's answer.**
**The ansewer comes from [boost::tokenizer vs boost::split](https://stackoverflow.com/questions/7930796/boosttokenizer-vs-boostsplit).**

The best choice depends on a few factors. If you're only needing to scan the tokens once, then the boost::tokenizer is a good choice in both runtime and space performance (those vectors of tokens can take up a lot of space, depending on input data.)

If you're going to be scanning the tokens often, or need a vector with efficient random access, then the boost::split into a vector may be the better option.

For example, in your "A^B^C^...^Z" input string where the tokens are 1-byte in length, the boost::split/vector<string> method will consume at least 2*N-1 bytes. With the way strings are stored in most STL implementations you can figure it taking more than 8x that count. Storing these strings in a vector is costly in terms of memory and time.

I ran a quick test on my machine and a similar pattern with 10 million tokens looked like this:

    boost::split = 2.5s and ~620MB
    boost::tokenizer = 0.9s and 0MB

If you're just doing a one-time scan of the tokens, then clearly the tokenizer is better. But, if you're shredding into a structure that you want to reuse during the lifetime of your application, then having a vector of tokens may be preferred.

If you want to go the vector route, then I'd recommend not using a vector<string>, but a vector of string::iterators instead. Just shred into a pair of iterators and keep around your big string of tokens for reference. For example:
'''
using namespace std;
vector<pair<string::const_iterator,string::const_iterator> > tokens;
boost::split(tokens, s, boost::is_any_of("^"));
for(auto beg=tokens.begin(); beg!=tokens.end();++beg){
   cout << string(beg->first,beg->second) << endl;
}
'''
This improved version takes 1.6s and 390MB on the same server and test. And, best of all the memory overhead of this vector is linear with the number of tokens -- not dependent in any way on the length of tokens, whereas a std::vector<string> stores each token.
