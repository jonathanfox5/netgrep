--8<-- "pages/solutions_working/gralhix_preamble.md"

## Comparison to Other Solutions

I picked this task as one of my first since finding missing data and understanding how websites work are the only OSINT skills that I have lots of experience in. This turned out to be quite handy since the ref

- You can
- It shows how to use the Wayback Machine API for more precise queries (sounds scary...don't worry, it isn't!)

## Task A: Finding the page

You cannot click the thumbnail to start the exercise. Clicking into other challenges, I noticed that they all share the same format. I therefore took the url format from the osint-019 challenge page and changed it to <https://gralhix.com/list-of-osint-exercises/osint-exercise-020/>.

Upon opening the page, a fake 404 error was given. This was clearly a clue. (You could also consider the embedded walkthrough YouTube video below it a little bit of a giveaway too =D ) The wayback machine is my default solution for pages that have been replaced with 403s / 404s:

```
!wbm https://gralhix.com/list-of-osint-exercises/osint-exercise-020/
```

I grabbed the oldest result and it contained a task briefing:

> The internet is a digital ecosystem in constant transformation. Websites change appearance, domains change owners, businesses open and close, and accounts are created and deleted.
> In July 2023, x.com went from being an almost blank page to redirecting to twitter.com.
> Your task is to go back in time, until the year 2000, and find the following information within the x.com website:
>
> b) The Frequently Asked Questions page.
>
> c) The list of members of the management team in July.

## Task B: FAQ Page

I opened the first result from 2000 in the Wayback Machine:

```
!wbm x.com
```

I clicked the FAQ link on the homepage but got a 404 error. I loaded up the FAQ page in calendar view:

```
!wbm http://www.x.com/help_faq.asp
```

All instances of this were coloured orange, i.e. they were all 4xx errors.

Going back to the main page and clicking a few links, I noticed that there are is a mix of .htm / .html / .asp links on the page. (htm / html can be thought of as the "static" versions and "asp" as the active version with extra code for the server to run... it's slightly more complicated in reality). Given this is something I've messed up when building websites in the page, I tried changing the extension and discovered that `.htm` worked:

```
!wbm http://www.x.com/help_faq.html
```

(no results)

```
!wbm http://www.x.com/help_faq.htm


```

Found link to the latest archived version of the FAQ before it was taken down: <https://web.archive.org/web/20000618112127/http://www.x.com/help_faq.htm>

## Task C: Management Team

### Getting Some Context

I saw there there was lots of news linked so I decided to get some historical context and potential keywords from the [Wikipedia page for x.com](<https://en.wikipedia.org/wiki/X.com_(bank)>) before continuing. Notes:

- "[Elon] Musk invested about $12 million into co-founding X.com in March 1999 with Harris Fricker, Christopher Payne, and Ed Ho"
- "five months after X.com had started, and the other two co-founders, Payne and Ho left consequently"
  - This would have been in 1999 (i.e. prior to July 2000)
- "In March 2000, X.com merged with its competitor Confinity [...] Musk was its biggest shareholder and was appointed as its CEO"
- "In September 2000 [...] the X.com board voted for a change of CEO from Musk to Peter Thiel"

Conclusion:

- **Assuming** Wikipedia is correct, the July 2000 list should have Musk as the CEO. This is a fairly narrow time window (6 months)

### Tracking Down the List

#### Searching the website: Dead end

I opened the July 2000 version of the website: <https://web.archive.org/web/20000707003958/http://x.com/>

- Checked news section (which links to external sites such as Forbes, FT, etc.) to see if any announcements were made e.g. in March merger
  - Link to Forbes report from 2000-03-03, claims that Bill Harris was CEO at the time, contradicting wikipedia research. Possible change was made shortly after.
    - "says Elon Musk, founder of X.com, who will continue to be chairman of the merged firm"
    - "Bill Harris, former chief executive of Intuit (nasdaq: INTU) who has been CEO of X.com, will head the merged company."
  - Newsposts from 2000-04-10 states "Peter Thiel, founder of PayPal.com and senior vice president of business development at X.com"

I opened all links on July 2000 to scan for information. Eventually found a [news release on the merger](https://web.archive.org/web/20000619231525/http://www.x.com/news_paypal.htm) dated 2000-03-02:

> Mr. Musk will remain Chairman, and current X.com President and CEO Bill Harris will retain those positions. Mr. Thiel will become Executive Vice President of Strategy, and PayPal Chief Technology Officer Max Levchin will retain that position for the new X.com Corporation.

I spent some time re-reading the FAQ page for clues but wasn't able to find anything. This included looking through the html for `<a` to see if there were any links that weren't immediately visible.

#### Working around Wayback Machine limitations

What I wanted to do next was find a list of all URLs that WBM had archived. Normally, I would do this via the built in URL tab but I couldn't find a way to filter it by date and the volume of pages on the current x.com meant that results were being truncated to 10000, meaning that pages that I knew existed in 2020 weren't being shown.

A bit of googling later, I found that when you query via their API, you can filter by date and also do some limited wildcarding. The main wildcarding limitation is that you can only have it in certain places, depending on what you set `matchType=` to.

The other limitation is that you need to be quite specific with your search e.g. `url=x.com/` times out but `url=x.com/news` does not. (NB: `url=x.com` will just resturn results for the homepage, you need the extra `/` to search for subpages)

Once you have written our your query, you can paste it into your address bar like any other website address. Example of a working query:

```yaml
http://web.archive.org/cdx/search/cdx?url=x.com/news&matchType=prefix&from=200006&to=200008

In the above, you can see that the parameters are:
url=x.com/news # website to search
matchType=prefix # add a wildcard to the end of the url
from=200006 # start date
to=200008 # end date

One of the results is the news_paypal page that we read earlier:
com,x)/news_paypal.htm 20000619231525 http://www.x.com:80/news_paypal.htm text/html 200 KD3CZJ5SSVEJKUN36K56PUBCPEWP4NI6 3327
```

I then substituted the value of the `url` parameter with various guesses for pages that might help us:

- x.com/management
  - http://x.com:80/management.html found but 404 for asp / html / htm
- x.com/our (trying to find something like "our_team")
  - No results
- x.com/team
  - No results
- x.com/about
  - Found `about_management.htm` (finding this URL was a fluke, I was expecting to just find `about.htm` or similar)
  - Expanding the `from` and `to` parameters to cover the whole year shows that this was first published in March 2000 with the last crawl in July 2000
  - A quick check in the normal web interface of Wayback Machine showed that the March and July versions are different, the next step is to work out when it was last changed since it's undated.

For reference, the query that found the July version of `about_management.htm` is <http://web.archive.org/cdx/search/cdx?url=x.com/about&matchType=prefix&from=200006&to=200008>. (you can click this link to run it)

The result of the query is:

```hl_lines="4"
com,x)/about_barclays.htm 20000609044804 http://www.x.com:80/about_barclays.htm text/html 200 HV5P427WYGXPRB2LUKRZAGZLSQWIB54W 2034
com,x)/about_barclays.htm 20000614110213 http://x.com:80/about_barclays.htm text/html 200 2XCZY6NZZLMVGOONH42IINZ3J6532ZCZ 2037
com,x)/about_management.htm 20000609122156 http://www.x.com:80/about_management.htm text/html 200 FS7E6WPAF3NIN356EAQXWYOPJKI3LP7O 3757
com,x)/about_management.htm 20000706205553 http://www.x.com:80/about_management.htm text/html 200 PAHVVTGGKLHPKTYTXR245DWTSWLSSQOC 3757
com,x)/about_management.htm 20000816111914 http://www.x.com:80/about_management.htm UNKNOWN 404 C5GT642E2GQIMGG2QPIBPQ6LKGFA2V6S 470
com,x)/about_promotions.htm 20000609144408 http://www.x.com:80/about_promotions.htm text/html 200 GMFVTUH2UVY3O3VVCNDSIAUR7CQHJMJP 1509
```

!!! note

    You could probably get to the same place using the URL tab of wayback machine. You would need to be very specific with your keywords to avoid hitting the 10000 result limit which truncates your results.

#### Verifying the list

I pasted the text into a [diff checker](https://www.diffchecker.com) to understand how old the text is, using the July version as the reference.

| Date       | Change                                                                     |
| ---------- | -------------------------------------------------------------------------- |
| 2000-07-06 | Reference                                                                  |
| 2000-06-09 | Identical                                                                  |
| 2000-05-11 | Bill Harris is in this version, Peter Thiel isn't listed, Musk becomes CEO |

The July list was therefore uploaded at some point after 11th May and before 9th June. It therefore superceeds the March information previously found.

Based upon this, the team are Elon Musk, Peter Thiel, John T.Story, Dave Johnson, Kathy Donovan, Sanjay Bhargava, Mark Sullivan.

It's

## Final Answers

| Letter | Item            | Answer                                                                                                       |
| ------ | --------------- | ------------------------------------------------------------------------------------------------------------ |
| a      | Website         | <https://web.archive.org/web/20230828083935/https://gralhix.com/list-of-osint-exercises/osint-exercise-020/> |
| b      | FAQ             | <https://web.archive.org/web/20000618112127/http://www.x.com/help_faq.htm>                                   |
| c      | Management team | Elon Musk, Peter Thiel, John T.Story, Dave Johnson, Kathy Donovan, Sanjay Bhargava, Mark Sullivan            |
