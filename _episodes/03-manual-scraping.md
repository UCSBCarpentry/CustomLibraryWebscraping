---
title: "Manually scrape data using browser extensions"
teaching: 45
exercises: 20
questions:
- "How can I get started scraping data off the web?"
- "How can I use XPath to more accurately select what data to scrape?"
objectives:
- "Introduce the Chrome Scraper extension."
- "Practice scraping data that is well structured."
- "Use XPath queries to refine what needs to be scraped when data is less structured."
keypoints:
- "Data that is relatively well structured (in a table) is relatively easily to scrape."
- "More often than not, web scraping tools need to be told what to scrape."
- "XPath can be used to define what information to scrape, and how to structure it."
- "More advanced data cleaning operations are best done in a subsequent step."
---

# Using the Scraper Chrome extension

Now we are finally ready to do some web scraping. For this lesson, we will be using two UCSB department webpages: East Asian Languages and Cultural Studies and Jewish Studies. We are interested in scrapping contact information from faculty within these departments with the help of Xpath and Scraper. To do so, we will use the Scraper extension in the Chrome browser (refer to the [Setup](FIXME) section for help installing these tools).
First, let’s focus our attention on the East Asian Languages and Cultural Studies webpage [https://www.eastasian.ucsb.edu/people/faculty/](https://www.eastasian.ucsb.edu/people/faculty/). 

We are interested in downloading the list of faculty names and their email addresses.
![East Asian Studies website]({{ page.root }}/fig/Image1.png)
	
## Scrape similar 
With the extension installed, we can select the first row in the faculty list, do a right-click and choose “Scrape similar” from the contextual menu. 

![East Asian Studies website/scrape]({{ page.root }}/fig/Image2.png)
 
You can select the picture as well. Make sure you do not right-click on a hyperlinked text. 
Alternatively, the “Scrape similar” option can also be accessed from the Scraper extension icon:
 
![Scraper Web Browser]({{ page.root }}/fig/Image3.png)
 
Either operation will bring up the Scraper window:

![Scraper web browser with red and blue boxes]({{ page.root }}/fig/Image4.png)
 
We can notice that Scraper has generated XPath queries that correspond to the data we had selected upon calling it. The Selector (highlighted in blue in the above screenshot) has been set to //tr[td] which selects all the rows of the table, delimiting the data we want to extract.
In fact, we can try out that query using the technique that we learned in the previous section by typing the following in the browser console:

> ## #Tip: Use the following shortcuts to Open Console:
>
> Panel Mac (Command+Option+J) Windows/Linux (Control+Shift+J). 
>Remember: ```<tr>``` defines a row in a table and ```<td>``` defines a cell is a table
>``` $x("//tr[td]")```
{: .callout}

The query will return something like:
~~~
<- (48)
~~~
{: .output}

Which we can explore in the console and check for highlights to make sure this is the right data.

Could you guess why we got 48 as a result?

There are 24 rows with faculty profiles, but in between them we had tr box shadows, if we unselect “Exclude empty results” which is set by default, we will get empty rows in our output. So it is wise to keep this option always selected.

Scraper also recognized that there were four columns in that table, and has accordingly created four such columns (highlighted in red in the screenshot), each with its own XPath selector, ```*[1]```, ```*[2]```, ```*[3]``` and ```*[4]```.

To understand what this means, we have to remember that XPath queries are relative to the current context node. The context node has been set by the Selector query above, so those queries are relative to the array of tr elements that have been selected.

We can replicate their effect by trying out the following expression in the console:
~~~
$x("//tr[td]/*[4]")
~~~
{: .source}

This should select only the first column of the table. The same goes for the second column.

But in this case, we don’t need to fiddle with the XPath queries too much, as Scraper was able to deduce them for us, and we can copy them into the clipboard in Tab Separated Values (TSV) format for pasting into a text document or a spreadsheet.

There is a bit of data cleaning we might want to do prior to that, though. 

1. The first column is empty because we have selected the photo and scraper recognizes that as an element, however, images are not included in the scrapping process, so we can remove it using the red (-) icon and click on scrape to see the change. Let’s do the same thing with column three because we are not interested in their positions or specialties now.
1. We also want to rename the other columns remaining accordingly, so let’s change them to Faculty_name and Contact_info.

## Custom XPath queries
Sometimes, however, we do have to do a bit of work to get Scraper to select the data elements that we are interested in.

Note that we still have other info such as office location and times along with emails. So what if we want to get a column only with emails instead? We should add a new column and rename it as Email and use Xpath to help us to refine that. To add another column in Scraper, use the little green "+" icon in the columns list. 

Let’s inspect the link to identify on the developer’s console the exact path for the email addresses. Select the email > right-click (make sure to not click in the email) > Inspect. Then, hover the mouse over the email > right-click > copy > copy Xpath. Note that there will be an option to copy the Full path but you do not need that as we have already scrapped from a portion of the website.

> ## Tip:
> You can copy the path to a notepad, it will help you to compare it with scrap and understand better where the element you are interested in is located. 
You should have the path bellow or something slightly different if you have selected other faculty email as the tr 
>[row number] will represent the data you have selected:
>
>~~~
>//*[@id="site-main"]/div/div/div[2]/div/table/tbody/tr[1]/td[4]/a 
>~~~
>{: .source}
{: .callout}
 
 
> ## Challenge: Scrape Emails
> Which path would you have to provide to Scraper to get the emails in one column?

Answer:
You should get a column with emails with the following path expression after hitting scrape
./td[4]/a
 
<output?>

Note that Scraper gave you a starting path based on what you have scraped `//tr[td]`, so you have only to add the continuation of it. In order to tell Scraper extension we are only interested in the emails, we will have to indicate the data that is in the fourth column and add the specific path to the email address. Don’t forget the dot (.) in the beginning of the Xpath expression. As we have learned in the previous lesson that is how you tell the path is in the current context node.

You can remove the contact row now and copy the output to the clipboard. 
{: .challenge}

## Let's scrape a different site.


Now let’s turn to the Jewish Studies faculty webpage [www.jewishstudies.ucsb.edu/people](https://www.jewishstudies.ucsb.edu/people) for practicing XPath queries a little bit more.

Note that the profiles on the Jewish Studies facultly page are laid out differently that the East Asian Studies faculty page. On the Jewish Studies page the information is not displayed in well-defined rows. So, when we scrape the web page data, we should get one row per faculty with one string of data.



![Jewish Studies website]({{ page.root }}/fig/Image5.png)
![Scraper Jewish studies]({{ page.root }}/fig/Image6.png)

If we want to have this data in a more reusable format, we will have to create columns indicating the exact path we want to scrape the data from, considering that these paths will be a continuation of the one highlighted in the image above. 

For this particular case, we want to have four columns: 

| 1. Name | 2. Email | 3. Position| 4. Office Location. |

Using the function to inspect where the element is located on the webpage, identify the correct paths, and scrape the information we need. 

For the first column __"Name"__ we will have to inspect where the name is located to get the right path to it. 

Select one of the Faculty names > right-click > inspect. It will open the developer window as indicated below:


![Jewish Studies with developer window]({{ page.root }}/fig/Image7.png)

In the developer tools window select the  html element containing the faculty name, right-click, then choose Copy Xpath. 
 
You should get this path: 

~~~
//*[@id="block-system-main"]/div/div/div/div/section[1]/div/div/div/div/div[1]/div[1]/span 
~~~
{: .output}



> ## Note 
>
> that you only have to specify in the expression things that are not included in the original XPath automatically created by Scaper. 
> Compare the two and see how we can express the path to Scraper. 
>
> {: .source}
{: .callout}




> ## Challenge: Why do both these Xpaths work?
> Question:  In this case, either of the following paths would work. Do you know why?: 
> 
> ```
> ./div[1]/span
> ./div/span
> ```
>
> Alternatively, you can also get it right if you use:
>
> ~~~
> ./div/span/a 
> ./div[1]/span/a
> ~~~
>
>
> > ## solution
> > In the first case telling that the element in the first node of that particular div is consistent throughout other names on the website. Omitting it won’t change the outcome, as you are describing that you are interested in the content of that div child node. 
> > If you provide the path with the extra `/a` you are telling Scraper to get the information that is in another child node, which happens to also include the faculty name, linking to a bio webpage. Again, including `[1]` or not does not change the outcome. 
> >
> >![Jewish Studies Scraper with 4 paths leading to the same result]({{ page.root }}/fig/Image8.png)
> {: .solution}
{: .challenge}


> ## Challenge: Scrape Three Columns
>
> Now that you have learned how to get the right path to create columns for names, follow the same steps to get the three other columns Emails, Position, and Office Location.
>
>
>
> After completing all steps you should get the following output:
>
> ![Screenshot of the Scraper]({{ page.root }}/fig/Image9.png)
>
> > ## Solution
> > 
> > ~~~
> > ./div[4]/div[1]/div/a (Email)
> > ./div[2]/div[2]/div/d (Position)
> > ./div[4]/div[3]/div/d (Office)>
> > ~~~
> {: .solution}
{: .challenge}



