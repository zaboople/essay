# Empty List & 200, Not 404

Let's say that every morning your application calls a URL and gets to look up relevant news articles for you. On some mornings, you get a 200 and a list of data. On other mornings, you get a 404, because 404 means "not found", right? That just means that for today there are no relevant articles. Everything is fine, come back tomorrow and maybe there will be something interesting.

Weeks go by, and you wonder, why don't I get any news articles? My application doesn't show any errors. I used to get lots of good articles. Hmph. Oh well. Many months after that, you find out that you have been getting 404's all this time because the URL for articles API is _no longer available_. Arguably, they should have redirected, but that didn't happen.

People often assume that since 404 means "not found", it also means "nothing found"... but that's not the same thing. If it's implicit that your URL returns 0-to-many things, then when it's 0, you should return an empty list and a 200 - not 404.

----

[Back to Software main page](./README.md)
