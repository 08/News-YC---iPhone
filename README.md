News-YC---iPhone
================

The iPhone version of News/YC, a Hacker News reader and interactive iOS application. 

![ScreenShot](https://raw.github.com/bennyguitar/News-YC---iPhone/master/screens.png)

## About ##

News/YC is a front-page reader for Hacker News (http://news.ycombinator.com), a portal for interestingness regarding technology and building things. This app is free, and will forever remain free - and now, starting with version 2.0, is entirely open-sourced (please don't laugh at my code).

## The Code ##

The root ViewController, App Delegate, and HNSingleton are in the top-level directory, while every other class should be self-documented through the folders they are in (Webservice, Data Objects, Utilities, etc.).

#### Webservice.{h,m} ####

This class contains all web requests to the API, using a delegated system so ViewController can receive callbacks about the success or failure of each call - as well as the objects (posts/comments) returned.

```objc
-(void)getHomepage;
```

Fairly self-explanatory, this method hits the API and pulls the homepage back as Post objects, then calls the delegate method <code>-(void)didFetchPosts:(NSArray *)posts</code> to return objects back to ViewController. If no posts are retrieved, or there is a database/server error, <code>nil</code> is returned through the delegate method. ViewController checks off of this for exceptions and displays the posts accordingly.

```objc
-(void)getCommentsForPost:(Post *)post launchComments:(BOOL)launch;
```

This method returns an NSArray of Comment objects using the delegate method <code>-(void)didFetchComments:(NSArray *)comments forPostID:(NSString *)postID launchComments:(BOOL)launch</code> to callback to ViewController. The launchComments part of this method is for showing the commentsView in ViewController. If YES, this will animate commentsView coming up from the bottom, if NO, this is a pull-to-refresh case where the data was updated and only the table needs to be refreshed. This method will also return <code>nil</code> if there is a database/server retrieval problem so we can handle the exception accordingly.

#### Data Objects - Post.{h,m} and Comment.{h,m} ####

These classes make up the data model used by News/YC. Both Post and Comment contain special constructor methods based on the JSON objects that return from <code>Webservice.{h,m}</code> calls.

```objc
@interface Post : NSObject

// Properties
@property (nonatomic,retain) NSString *Username;
@property (nonatomic, retain) NSString *URLString;
@property (nonatomic, retain) NSString *Title;
@property (nonatomic, assign) int Points;
@property (nonatomic, assign) int CommentCount;
@property (nonatomic, retain) NSString *PostID;
@property (nonatomic, assign) BOOL HasRead;
@property (nonatomic, retain) NSDate *TimeCreated;
```

```objc
@interface Comment : NSObject

// Properties
@property (nonatomic, retain) NSString *Text;
@property (nonatomic, retain) NSMutableAttributedString *attrText;
@property (nonatomic, retain) NSString *Username;
@property (nonatomic, retain) NSString *CommentID;
@property (nonatomic, retain) NSString *ParentID;
@property (nonatomic, assign) int Level;
@property (nonatomic, retain) NSDate *TimeCreated;
@property (nonatomic, retain) NSMutableArray *Children;
@property (nonatomic, retain) NSMutableArray *Links;
@property (nonatomic, assign) CommentType CellType;
```

## API ##

News/YC uses the only officially sanctioned API that Hacker News approves of: http://hnsearch.com/api. The API calls made by this app are slightly tricky. For the front-page posts, a call is made to http://hnsearch.com/bigrss and then the resulting xml is parsed to grab the unique IDs from each post. Those are then sent as a request to the actual API to get the posts. The comments, unfortunately, return as basically a totally unordered set. I create a linked list out of those, and then I turn it into a flat array based on the nested nature of the comments - so UITableView will render correctly.

## 3rd Party Libraries ##

News/YC uses the following third party libraries:

* IIViewDeck - https://github.com/Inferis/ViewDeck

## Things to Come ##

For subsequent versions, I'd like to see a couple things happen to make this app awesome.

**Features**

* Logging In
* Voting
* Submitting
* Commenting
* Viewing Ask HN / Newest Posts

**In Code**
* Comment labels using NSAttributedString (for < html > tags)
* Cleaned up a little bit / better documentation

