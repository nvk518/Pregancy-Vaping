# Pregancy-Vaping
## Full Keyword Data EDA Methodology
1. Find relevant search key words that correspond to pregnancy and vaping. The search terms used were “Vaping pregnant”, “Vaping pregnancy”, “Vape pregnant”, “Vape pregnancy”, “E-cigarette pregnant”, and “E-cigarette pregnancy”.
2. Used a TikTok video scraper on a platform called Apify (https://apify.com/novi/fast-tiktok-api) that takes in various inputs. (See figure below) 
    * Scraping type: SEARCH
    * Region code: US
    * Keyword: {one of the 6 aforementioned keywords}
    * Limit: 3000
    * Sort Type: Relevance
    * Publish Time: All-time
![Alt text](/pregnancy-vaping/images/scraper_query.png?raw=true "Title")

3. I queried for each of the 6 keywords. I set a high limit due to several factors:
Scraping data can be difficult due to TikTok’s rate limits, so not every video can be scraped
The scraper could scrape multiple copies of the same video
Overlapping of duplicate videos between the different keywords
After executing the query, I was left with between a varying number of videos for each keyword and exported metadata in the form of an Excel spreadsheet. I chose columns that provided relevant information about the video, statistics, description, and audio. I noticed that some metadata sheets had more columns than others. I saved this data as an Excel file (.xlsx). Keyword scraped:
    * Vaping Pregnancy: 270
    * Vaping Pregnant: 316
    * Vape Pregnancy: 257
    * Vape Pregnant: 154
    * E-cigarette Pregnant: 204
    * E-cigarette Pregnancy: 309
    * Total entries: 1510
4. I created Python scripts on Google Colab for deduplication and ensuring that videos aren’t repeated.
    * Extract the Video ID from the Share URL and create new video_id column
    * Format the Share URL and replace share_url column
    * Remove duplicated Video IDs in each Excel sheet (ie. Video ID 123 showing up twice in the “Vaping pregnant” sheet)
5. Iterated through each XLSX spreadsheet. Created a Python dictionary key-value data structure as a mask. I filtered out any video_ids from the sheet that were in this dictionary and added all resulting Video IDs to the dictionary. I had to reorder the order in which I parsed through the spreadsheets as there were a lot of duplicates and some resulting spreadsheets ended up with less than 100 videos. Eventually, I found an order where they all had more than 100 videos.  (See figure below) 
![Alt text](/pregnancy-vaping/images/post_deduplication.png?raw=true "post deduplication")


    * I also checked to ensure that video_ids were not repeated at all and were unique after steps a-d were performed. (See figure below) 


1. I used a bulk TikTok video downloader and inputted Share URLs in batches of 30. Because some videos had been either taken down, deleted, or made private, they could not be downloaded, so I had to manually delete rows from the metadata sheet.
2. I cleaned the metadata sheet and the column names. I deleted columns that contained mostly 0’s, had empty columns, and were irrelevant. 
    * To remove columns with empty data, I calculated the sum of each column and removed those in which the sum was 0 for at least 3 of the 6 keyword files. (See figure below) 


    * I added irrelevant columns into a List, and iterated through them to see if each column contained a substring present in the List. (See figure below)  



    * I noticed that because of the way the metadata was scraped, some sheets had more columns to begin with than others. I had to create a set of a each DataFrame’s columns, then used an intersection function to find the common columns of the 6 column. In total, all my changes and deletions reduced the number of columns from between 383 to 425 down to 85 in the cleaned version. (See figure below for code)



8. I further cleaned the columns by removing the hierarchical structure of the column names. Because the data was converted from a JSON format to XLSX, I had to remove “/”. I did this by splitting each column name by the “/” character and choosing the last element, effectively removing the tree structure and choosing the leaf. (See figure below) 



9. I noticed that there was a flaw in the scraper where many video descriptions had not been properly parsed to extract their hashtags into their own columns. I circumnavigated this problem by applying a regex function to obtain hashtags from descriptions and saved them as a new column of a set of tags. I used a set rather than a list because I noticed that some hashtags were being repeated in the same video description.
10. I created another column for whether a video was a reply to another video by using regex to extract the phrase “Replying to @” from the video description.
11. Ultimately, the number of columns was reduced significantly. The figure below shows the effect of removing columns that were incorrect, irrelevant, null, empty, or redundant.







## Column Documentation:
* video_id: TikTok’s unique identification for videos
* description: video description provided by the poster
* allow_comment: author has set the TikTok to allow comments (TRUE = yes, FALSE = no)
* reviewed: (unsure) TikTok has been reviewed
* comment_filter_status: has an active comment filter (block out certain keywords)
* commerce_user_level: 2 if author is e-commerce owner/business; Otherwise 0
* custom_verify: Author is verified 
* enterprise_verify_reason: Author is verified as a business
* follower_count: Number of users following author
* following_count: Number of users the author is following
* ins_id: Instagram username if author has added to their profile
* mention_status: Unsure, possibly whether the author has mentions allowed (1) or blocked (4). If blocked, this user cannot be tagged on another TikTok using ‘@’
* nickname: Author’s nickname 
* status: Unsure, whether the video is still up on TikTok
* stitch_setting: If 3, the video cannot be stitched by another user. Otherwise, it can be stitched
* unique_id: Author’s username
* verification_type: Unsure, possibly how the account was verified (ie. phone number)
* share_desc: description used when sharing video
* share_url: full share url for video
* create_time: timestamp for video creation in an special format (To extract: https://dfir.blog/tinkering-with-tiktok-timestamps/)
* search_desc: text present in the video content itself
* region: Region of world (ie. US = United States, GB = Great Britain, DE = Denmark, CA = Canada)
* favorited_count: Number of users who have favorited/saved the TikTok
* comment_count: Number of comments made by other users on the TikTok
* likes_count: Number of times this video has been liked
* download_count: Number of times this video has been downloaded
* play_count: Number of times this video has been played (one viewer can view multiple times)
* share_count: Number of times this video has been shared (ie. Twitter, to iMessage, link copied, etc.)
* whatsapp_share_count: Number of times this video has been shared to WhatsApp
* url: URL for video that can be shared
* hashtags: All hashtags present in the video description
* is_reply: Whether the video is a reply to a comment made in another TikTok


## Flowchart:
Draw.io file: https://drive.google.com/file/d/1CaVFKchkF-EBF9LmmKklxqI-25J4R_mW/view?usp=sharing 
