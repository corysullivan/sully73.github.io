---
layout: post
title: Logging with OSLogStore
tags: [xcode, framework]
---

As I previously mentioned, my new app [TimeStack](https://sullivans.ca/2023/01/05/ive-been-an.html) is nearing its release date. One of the final tasks on my to-do list is to include the ability to send logs as part of a support request. This requires a solution for persisting logs locally so that they can be attached to an email. In the past, I have used third-party frameworks like [CocoaLumberjack](https://github.com/CocoaLumberjack/CocoaLumberjack) to achieve this. However, I wanted to avoid adding another dependency and decided to test out Apple's OSLog framework. It includes the [OSLogStore](https://developer.apple.com/documentation/oslog/oslogstore) feature, which retrieves an array of log entries. This seemed like an ideal solution for my needs, but as I discovered, it has some issues. 

Peter Steinberger conducted an in-depth examination of OSLogStore in his article ["Logging in Swift,"](https://steipete.com/posts/logging-in-swift/) and highlighted its strengths and challenges. Despite initial issues, it appears that OSLogStore is now a reliable and robust tool for logging.

Fetching entries is as simple as using OSLogStore `getEntries` function.

```swift
let allEntries = try logStore.getEntries(
with: .reverse,
at: oneHourAgo,
matching: NSPredicate(format: "subsystem = %@", subsystem))
}
```

After implementing OSLogStore, I encountered several issues that caused concern. Firstly, logs were not being saved between app launches, and secondly, retrieving logs took an excessive amount of time. In particular, on two devices (iPhone 13 and iPhone XR), fetching logs consistently took over 10 seconds.

<img src="https://sullivans.ca/uploads/2023/ecf143bc2f.png" width="600" height="361" alt="" />

Upon researching the issue, I found no similar complaints online. I reached out to Apple Technical Support for assistance, but the engineer could not provide a clear explanation. He did mention that the system log is optimized for writing, not reading and that the log size can vary significantly between devices, making it difficult to pinpoint the cause of the slow retrieval time.

> The system log is optimized for writing, not reading.

Given the uncertainty surrounding the performance of OSLogStore, I decided to temporarily set it aside and re-evaluate it at the next WWDC conference.
