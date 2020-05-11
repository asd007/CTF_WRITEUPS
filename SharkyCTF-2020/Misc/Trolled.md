# Problem Statement

Tough to write-up this one, no statement, The challenge was that the CTF site was briefly displaying a popup for the challenge which was set to cleverly change the browser location to / through an onerror tag in a img whoise href contained the flag itself (thus causing an error, though there were other sources of errors too).

The solution was to break on DOM beforeunload and javascript console the answer in.

I'll do my best to write this up soon.
