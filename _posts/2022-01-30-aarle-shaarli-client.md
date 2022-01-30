---
layout: post
title: "aarle - a Shaarli / Pinboard bookmark manager"
date: "2022-01-30 14:50"
---
> Can you even call yourself an iOS Engineer if you never shipped a Twitter or Pinboard client?

On the 2nd of January, I migrated my bookmarks from Safari / Pinboard to a self-hosted [Shaarli](https://github.com/shaarli/Shaarli) instance. I generally prefer native Apps over web interfaces but couldn't find a client for iOS/macOS. Time for a tiny new App.

## aarle
![iOS Screenshot of aarle](https://share.hartl.co/aarle-macos.png)

![iOS Screenshot of aarle](https://share.hartl.co/aarle-ios.png)

aarle is pure SwiftUI App for macOS and iOS. The feature set is the bare minimum:
- Log in using a URL and the secret of your Shaarli instance
- Browser your bookmarks
- Filter your bookmarks by tag
- Declare favorite tags shown in the sidebar / start screen
- Basic Search
- Add / Edit bookmarks
- Share extension to add bookmarks

Only yesterday I decided to also add basic Pinboard support as the APIs are very similar:
- Log in using your Pinboard API key (can be found on the Pinboard settings)
- Basic search of your last 1000 entries

The backlog for minor improvements is already overflowing. As a next big feature, I would like to add offline read-it-later support using webarchives. Because why not.

## Testflight
I hope to submit the App to App review in a few days, in the meantime you can join the Testflight groups:
- [iOS Testing Group](https://testflight.apple.com/join/UkyyBgsu)
- [macOS Testing Group](https://testflight.apple.com/join/4mGnXqXf)

## The code
I'm not spending time on another hot-take how buggy SwiftUI is. I really enjoy working on this rather basic App and currently have no interest in building another UIKit/Catalyst App in my free time. It was also my first time spending more than a few minutes on SwiftUI for macOS. [AppKit is Done - kean.blog](https://kean.blog/post/appkit-is-done#share) was an amazing timesaver for this.
The code itself is only functional, not clean, not dry, not covered with tests. Nothing to be proud of in itself, but I enjoy the App and that's all that mattered here for me.
If you are interested, it's [open source](https://github.com/hartlco/aarlo).
For the basic UI architecture: A few stores that contain a redux-like flow are passed to the views as `EnviornmentObject`. The views listen to the store's state changes and update it using actions.
