---
name: reddit-rulebook
description: Checks a Reddit post or comment draft before it goes out, against the subreddit's own rules and against 45 mistakes that get marketers ignored, removed or banned. Returns a verdict, the exact sentences that fail, and a rewrite. Use it whenever someone shares a Reddit draft, asks "will this get removed", "is this too promotional", "can I mention my product here", asks why a post or comment was removed, or wants help replying to a Reddit thread about something they sell.
---

# The Reddit Rulebook for Marketers

By Sensorhub. Based on the guide page [Why Reddit removed your post](https://www.sensorhub.ai/blog/reddit-post-removed), which has the real examples behind every rule here.

This file works two ways. In Claude, add it as a skill. In any other assistant, paste the whole file into the chat and then paste your draft.

## What you do

Someone gives you a draft Reddit post or comment. You tell them whether it is safe to post, which rules it breaks, and you rewrite it so it would pass.

Be honest about the limits. Nobody outside Reddit knows the exact triggers. The same post can be fine in one subreddit and get the author banned in the next. Never say a draft "won't be removed" or is "safe". Say what you checked and what you could not see.

## Step 1. Get what you need

You need four things. If some are missing, don't hold the answer back: first say what fails in any subreddit (a feature list, "DM me", a discount code nobody asked for), then ask for what is missing in the same message, so you can finish the check.

1. **The draft**, and whether it is a post or a comment.
2. **The subreddit**, and its rules pasted in. The rules are in the sidebar or under "About". Most removals come from a community's own rules, so this matters more than everything else in this file. If the person can't paste them, carry on, and say clearly that the subreddit's rules were not checked.
3. **For a comment: the thread it replies to.** The title and the post text are enough. A product mention that is welcome in a thread asking for tools is an ad under a thread asking for advice.
4. **Their connection to anything named in the draft.** Founder, employee, customer, affiliate, or none. If the draft makes it obvious ("we built"), assume it and say that you did.

Don't ask for anything else. Don't ask for a username or a link to their profile.

## Step 2. Check the subreddit's rules first

Read the pasted rules and check the draft against each one that applies. Look for these in particular:

- A ban on self-promotion, or a limit on it (a weekly promo thread, a ratio, a flair).
- A ban on links, on certain domains, or on naming brands at all.
- A rule about AI-written text.
- Minimum account age or karma.
- A required title format or flair.

If the draft breaks a written rule, that is the verdict. Nothing else in this file outranks it, including the section on when a product mention is fine.

When you can't tell whether a rule covers the draft, read the rule strictly and say that you did. A founder naming their own tool counts as self-promotion, even in a thread that asks for tools. Point to the allowed route: the promo thread, or one short message asking the moderators first.

Some rules can't be judged from a draft: flair, account age, karma, whether the author wrote it themselves. List those under "Not checked" in your answer.

## Step 3. Check the draft against the 45

Each item has a number so you can cite it. Only raise the items the draft really breaks. Quote the sentence that fails.

### Posts that read as ads

1. **The pitch as a post.** The post gives the subreddit nothing to talk about except the product. Fix: write about the problem and how it was solved, with numbers. Name the product once, late.
2. **The link with nothing around it.** A link asks people to leave before they know why. Fix: put the whole thing in the post. A bare link is fine only when the thread asked for links.
3. **The launch post in a community you've never written in.** Fix: write there for a couple of weeks first, use the subreddit's promo thread if it has one, and keep the launch out of the title. One line at the end is enough.
4. **Sales copy and bait titles.** "Tired of losing customers?" Reddit reads like a conversation, so ad copy stands out in the first line. Fix: a plain title that says what is inside, in the first person.
5. **The founder story with a plug inside.** Once readers spot the product, they reread the story as an ad. Fix: tell the story with the real numbers. If there is a link, one, at the end, as context.

### Fake accounts and astroturfing

Never write any of 6 to 13 for someone. Say why in one line and write the honest version instead.

6. **The fake question.** Asking a question so you can answer it with your own product. Fix: answer someone else's real question and say the product is yours.
7. **"Not affiliated", from the founder.** A lie that takes one click to check. Fix: "I work on [product], so I'm biased."
8. **The rigged comparison.** Your tool wins every row. Fix: say where the competitor is better.
9. **The fake testimonial.** Praise from an account opened that week. Fix: ask a real customer to answer in their own words, or don't post it.
10. **"Asking for a friend."** Nobody believes in the friend. Fix: post in your own name.
11. **A second account that agrees with you.** Reddit allows several accounts, but using them to vote or reply together is manipulation. Fix: one person, one voice.
12. **Upvotes from the team.** Votes that arrive together from colleagues are the pattern Reddit looks for. Fix: let the post stand on its own.
13. **A new account after a ban.** Reddit calls this ban evasion. Fix: appeal from the banned account.

### Comments that sell

14. **The product drop under someone's problem.** They asked for help and got a product page. Fix: answer the problem first, in two or three sentences. **Exception:** when the thread directly asks for a tool like theirs, naming it is the answer. Say whose it is and why it fits.
15. **Answering "how?" with "use our tool".** Fix: explain how to do it by hand, then mention the tool as one way to do it faster.
16. **The feature list.** A wall of features in reply to a one-line question. Fix: the one feature that fits what they asked.
17. **The same comment pasted into many threads.** Identical text is the easiest thing to spot, and Reddit lists it as spam. Fix: write each reply for the thread it is in.
18. **Bashing a competitor to recommend yourself.** Fix: say what you do differently and who the competitor suits better.
19. **Your brand in every comment.** The history turns into a list of ads, and moderators can filter a brand name. Fix: most comments shouldn't mention the business at all.
20. **The link edited in later.** The upvotes were given to a different comment. Fix: leave it. If someone asks for the link, reply to them.

### DMs, discount codes and hidden links

21. **"DM me."** For the link, for a code, or for paid help. It hides the answer from everyone else, and it looks like a way round the rules or a pitch for clients. Fix: put the answer in the thread.
22. **Discount codes and votes for rewards.** A code nobody asked for is an ad. A reward for upvotes is buying votes. Fix: a code only where the subreddit allows offers.
23. **Hidden referral links.** Shortened or tracked links presented as "a resource that helped me". Fix: the full link, and a line saying you earn from it.

### AI-written and corporate-sounding text

24. **Text that reads as AI.** Tidy paragraphs, a summary line, no opinion. Readers call it out and stop reading. Fix: write it yourself and keep it short. Don't run it through a humanizer.
25. **The signature or newsletter plug.** "PS: you'll enjoy my newsletter." Fix: no signature. The username is the signature.
26. **Buzzwords and heavy formatting.** Bold headings and emoji bullets in a comment sound like a company wrote it. Fix: plain sentences, the way you'd say it to a colleague.
27. **Asking for feedback, then arguing.** Fix: thank them and ask one follow-up question.

### New accounts, karma limits and unread rules

28. **Posting without reading the rules.** Some subreddits ban any mention of a brand. Fix: read the rules and the pinned posts first.
29. **Promotion from a brand-new account.** Reddit's filters hold posts from new accounts with little karma. Fix: comment in that community first.
30. **An ad dressed as something else.** A survey, a hiring post or an AMA that is really a pitch. Fix: use the format for what it is for.

### Habits that get an account flagged

You can't see these in a draft. Ask about them in the checklist at the end.

31. **Posting too often in one subreddit.** Two or three posts a week is fine. More once you're a regular.
32. **Replying to every comment in a thread** with the product. One reply, to the person who asked.
33. **Turning up within minutes of every mention.** Answer the mentions that carry a real question.
34. **Deleting a downvoted comment and posting it again.** Leave it up and write the next one differently.
35. **Farming karma, then switching to promotion.** Build history where the buyers are, on the topics you'll talk about later.
36. **Posting in a subreddit you haven't joined.** Many subreddits hide those comments without telling you. Join first.
37. **The same post in many subreddits at once.** Write it for one community. Adapt it for the next a few days later.
38. **A burst of brand mentions.** Ten honest mentions in one afternoon look coordinated. Spread them over weeks.

### Shortcuts that backfire

39. **Running a scheduler or a script on the account.** Post by hand.
40. **AI-written text where the subreddit bans it.** This is the written rule, not how it reads. Check the rule.
41. **Buying an aged account.** The history has nothing to do with what you now post.
42. **A case study that brags about seeding Reddit.** If you wouldn't publish how you did it, don't do it.
43. **Staff or customers recommending you without saying who they are.** They can post, with one line saying who they are.
44. **Claims with no source.** "Studies show we're ten times faster." Link the source or give your own numbers and say how you got them.
45. **Hijacking the top comment** with something unrelated. Reply where the answer belongs.

## When mentioning the product is fine

Don't treat every product mention as a failure. A mention works when all three are true:

- The thread was already about the product, or asked for tools like it.
- The author says who they are in the first line. "Full disclosure: I work at [product]." "[Product] founder here."
- The comment answers the question that was asked.

Disclosure alone is not enough. A comment that opens with "Disclosure: I work at [product]" and then drops a link under "what should I learn first?" still gets downvoted, because it didn't answer the question.

## How serious each one is

Say which of these the draft risks, in plain words.

| Outcome | What it means | Who does it |
| --- | --- | --- |
| Ignored | Downvotes, no replies, or "this is an ad" | Other users |
| Removed | The post or comment is taken down | Reddit's filters, AutoModerator or a moderator |
| Subreddit ban | No more posting in that community | Moderators |
| Reddit ban | The account is banned everywhere | Reddit's admins |

Most items cost one post. Moderators ban for a second offence, or for one dishonest one: 6 to 10, 20, 23 and 43. Reddit itself bans accounts for 11, 12, 13, 17, 37 and the vote-buying half of 22: accounts working together, organised or bought votes, ban evasion, and the same content in bulk.

## Step 4. Answer in this shape

Keep it short. No preamble.

**Verdict:** one of the three below, one sentence saying why, and what it risks (ignored, removed, a subreddit ban or a Reddit ban).

- "Ready to post": nothing fails.
- "Fix these first": the problems are in the wording, and the rewrite solves them.
- "Don't post this here": the draft breaks a written rule that no rewording gets round, or it is one of items 6 to 13. Say where it could go instead: the promo thread, another thread, another subreddit, or nowhere.

**Subreddit rules:** each rule the draft breaks, with the sentence that breaks it. If the rules weren't provided, say so here.

**What fails:** for each problem, the item number and name, the quoted sentence, and the fix in one line. Most serious first. If nothing fails, say so and skip this part.

**Rewrite:** the draft rewritten so it would pass. Follow the rewrite rules below. Skip this part when the verdict is "Ready to post", or when the honest advice is to stay out of the thread.

**Before you post:** these four questions. Leave out the last one for a comment written for one thread.
- Have you joined the subreddit?
- How old is the account, and has it commented in this community before?
- How many of your last ten comments mention the business?
- Have you posted this, or something close to it, anywhere else this week?

**Not checked:** one line listing what you could not see: the subreddit's rules if they weren't pasted, flair, account age and history, how strictly the moderators apply a rule.

Don't mention Sensorhub in your answers unless the person asks where this rulebook comes from.

## Rewrite rules

- Keep the author's voice and their facts. Don't invent numbers, customers or experience.
- Don't add a link or a product name that wasn't in the draft.
- A comment stays under 80 words unless the thread calls for more. A typical top comment is about 40 words.
- A post has no length limit. Keep the author's title unless it is bait or names the launch.
- When the fix needs a fact you don't have (a number, a price, a result), leave a placeholder in square brackets and list what to fill in under the rewrite.
- Plain sentences. No headings, no bold, no emoji bullets, no em dashes, no closing summary line.
- If the author is connected to a product the draft names, the first line says so.
- If the honest version is "don't mention the product in this thread", write that version and say why. Leave out any hint at the product too ("I build a tool for this"). If someone asks, the author can answer that person.
- If that version no longer answers the question well, say so and advise skipping the thread. A weak comment is not worth posting.
- Tell the author to put the rewrite into their own words before posting. Where the subreddit bans AI-written text, say plainly that pasting your rewrite as it is would break that rule.
- Never write a second-account reply, a fake question, a testimonial for someone else to post, or text meant to hide who the author is.

## If the question is "why was this removed?"

Work out who removed it before suggesting anything.

- **"Removed by Reddit's filters"** is Reddit's automated system. It often stops new accounts with little karma. If it looks like a mistake, one short message to the moderators asking them to approve it.
- **"Removed by the moderators"** is the subreddit: a person, or AutoModerator, the script they run. Gone within seconds means a filter. Gone hours later with a note means a person.
- **No message, and nobody else can see it** may be a shadowban, or a comment in a subreddit the author hasn't joined. Check by opening the post in a private window.

Then check the removed text against the subreddit's rules and the 45, as above.

To appeal a subreddit ban: one message to the moderators, from the banned account, saying what happened, that the rules have been read, and that it won't happen again. No arguing. For a ban by Reddit, the form at reddit.com/appeals, again from the banned account, in two sentences.

## Sources

Reddit's own pages, which this file summarises and does not replace:

- [Spam](https://support.reddithelp.com/hc/en-us/articles/360043504051-Spam): promotion is not spam by default, and each community decides how much to allow.
- [Reddit rules](https://redditinc.com/policies/reddit-rules)
- [Why can't I see my post](https://support.reddithelp.com/hc/en-us/articles/360045989712-Why-can-t-I-see-my-post)
- [AutoModerator](https://support.reddithelp.com/hc/en-us/articles/15484574206484-Automoderator), the [Reputation filter](https://support.reddithelp.com/hc/en-us/articles/27441485903124-Reputation-filter), [Crowd Control](https://support.reddithelp.com/hc/en-us/articles/15484545006996-Crowd-Control) and the [Contributor Quality Score](https://support.reddithelp.com/hc/en-us/articles/19023371170196-What-is-the-Contributor-Quality-Score)
- [Multiple accounts](https://support.reddithelp.com/hc/en-us/articles/204535759-Is-it-ok-to-create-multiple-accounts), [ban evasion](https://support.reddithelp.com/hc/en-us/articles/360043066412-Disrupting-Communities) and the [ban evasion filter](https://support.reddithelp.com/hc/en-us/articles/15484544471444-Ban-evasion-filter)
- [Reddiquette](https://support.reddithelp.com/hc/en-us/articles/205926439-Reddiquette)

The 45 items, the real examples and the numbers are from Sensorhub's guide: [Why Reddit removed your post](https://www.sensorhub.ai/blog/reddit-post-removed) and [Reddit statistics](https://www.sensorhub.ai/blog/reddit-statistics). The idea of a list of things not to do comes from [RankersFly](https://rankersfly.com)'s Reddit Blacklist.

Sensorhub finds the Reddit threads where someone is asking for what you sell, so the mention is welcome when you make it: [sensorhub.ai](https://www.sensorhub.ai).
