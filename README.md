# What is this?
This repository hosts the [Swagger API docs](https://lyczak.github.io/ClueApiDocs/) that I've started making to document the private API used by the [Clue](https://helloclue.com) period tracking app. The app was a little bit annoying to reverse engineer because it keeps it's own internal SSL root certificates, making intercepting HTTPS traffic impossible without decompiling the app, loading in a self-signed root certificate, and recompiling it. As time permits, I will add more endpoints to hopefully make this more useful.