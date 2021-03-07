<p align="center"><img alt="repltak" src="https://repltalk-logo.codingcactus.repl.co/logo.png" /></p>


A ruby client for the [repl talk](https://repl.it/talk) gql api.

# Getting Started

## Gemfile
```
gem "repltalk"
```

## Initializing Client

```ruby
require "repltalk"

client = Client.new
```

Once you have your client initialized, you can start getting users, posts, comments etc.

***

# Code Snippets
A few small snippets of examples of what you can do with the repltalk gem

### Get CodingCactus' posts from the top 100 posts:
```ruby
client.get_posts(order: "votes", count: 100).select { |post| post.author.username == "CodingCactus" }
```

### Get the 10 most recent ruby post's URLs
```ruby
client.get_posts(count: 10, languages: ['ruby']).map { |post| post.url }
```

### See how many people have forked CodingCactus' classrooms repl
```ruby
client.get_repl("/@CodingCactus/classrooms").get_forks(count: 999999999).length
```

### See how many comments in a post mention CodingCactus
```ruby
mentions = 0
client.get_post(33995).get_comments(count: 999999999).each do |comment|
	mentions += 1 if comment.content.include?("@CodingCactus")
	comment.get_comments.each { |child_comment| mentions += 1 if child_comment.content.include?("@CodingCactus") }
	sleep 0.25 # need to be careful with rate limits
end
```

### See what position CodingCactus is in the all time leaderboard
```ruby
position = 0
client.get_leaderboard.each_with_index { |user, index| position = index + 1 if user.username == "CodingCactus" }
```

***
# All Methods Documentation

## Client
+ `#get_user username` Get a user from their username. Returns `User`
+ `#get_user_by_id id` Get a user from their id. Returns `User`
+ `#get_post id` Get a post from it's id. Returns `Post`
+ `#get_comment id` Get a comment from it's id. Returns `Comment`
+ `#get_repl url` Get a repl from it's url. Returns `Repl`
+ `#get_board name` Get a board from it's name. Returns `Board`
+ `#get_leaderboard :count, :since, :after` Get the users from the leaderboard. Since should be one of `PAST_24_HOURS, PAST_7_DAYS, PAST_30_DAYS, PAST_YEAR` (is all time by default). Returns array of `LeaderboardUser`s
+ `#get_posts :board, :order, :count, :after, :search, :languages` Get posts from repltalk. The languages argument should be an array of lamguage ids. Returns array of `Post`s
+ `#create_post board_name, title, content, :repl_id, :show_hosted` Create a repl talk post. Returns `Post`

## User
+ `#id` User's id
+ `#username` User's username
+ `#name` User's full name
+ `#pfp` URL of the user's pfp
+ `#bio` User's bio
+ `#cycles` How many cycles the user has
+ `#timestamp` When the account was made
+ `#is_hacker` Whether the user has the hacker plan
+ `#roles` User's roles. Returns an array of `Role`s
+ `#subscription` User's subscription. Returns `Subscription`
+ `#languages` Languages that the user has used. Returns array of `Language`s
+ `#get_posts :order, :count, :after` Get the user's posts. Returns array of `Post`s
+ `#get_comments :order, :count, :after` Get the user's comments. Returns array of `Comment`s
+ `get_repls :count, :order, :direction, :before, :after, :pinnedReplsFirst, :showUnnamed` Get the user's repls. Returns array of `Repl`s

## LeaderboardUser
Exact same as `User` exepts has `#cycles_since` which is show many cyces the user got since a certain time (24 hrs, 7 days, 30 days, 1 year, all time) depending on what type of leaderboard you have

## Post
+ `#id` Post's id
+ `#url` Post's url
+ `#repl` Repl attatched to the post. Returns nil if there is none. Else returns `Repl`
+ `#board` Board that the post is from. Returns `Board`
+ `#title` Post's title
+ `#author` Post's author. Returns `User`
+ `#content` Post's content
+ `#preview` Preview of the post's content.
+ `#timestamp` When the post was posted
+ `#vote_count` How many votes there post has
+ `#comment_count` How many comments the post has
+ `#answer` The comment that has been marked as the answer. Returns `nil` if there is none, else `Comment`
+ `#is_answered` Whether an answer has been selected
+ `#is_answerable` Whether you are able to answer the post
+ `#is_announcement` Whether the post in marked as an announcement
+ `#is_pinned` Whether the post is pinned
+ `#is_locked` Whether the post is locked
+ `#is_hidden` Whether the post is hidden (unlisted)
+ `#get_upvotes :count` Get the users that have upvoted the post. Count defaults to 10. Returns array or `User`s
+ `#get_comments :order, :count, :after` Get the post's comments. Returns array of `Comment`s

## Comment
+ `#id` Comment's id
+ `#url` Comment's url
+ `#author` Comment's author. Returns `User`
+ `#content` Comment's content
+ `#post_id` Id of the post that the comment is on
+ `#is_answer` Whether the comment has been selected as the answer to a post
+ `#vote_count` How many votes teh comment has
+ `#timestamp` When the comment was made
+ `#get_post` Get the post that the comment was made on. Returns `Post`
+ `#get_comments` Get the children comments of the comment. Returns array of `Comment`s
+ `#get_parent` Get the parent comment of a child comment. Returns `nil` if it ins't child, else `Comment`

## Repl
+ `#id` Repl's id
+ `#url` Repl's URL
+ `#title` Repl's name
+ `#author` Repl's author. Returns `User`
+ `#description` Repl's description
+ `#size` How many bytes the repl is
+ `#language` Repl's language. Returns `Language`
+ `#image_url` Repl image's url
+ `#origin_url` Url of the repl from which this repl was forked
+ `#is_private` Whetehr the repl is private
+ `#is_always_on` Whether the repl is always on
+ `#get_forks` Repl's forks. Returns array of `Repl`s
+ `#get_comments` Repl's comments. Returns array of `ReplComment`s

## ReplComment
+ `#id` Comment's id
+ `#content` Comment's content
+ `#author` Comment's author. Returns `User`
+ `#repl` Repl the comment was made on. Returns `Repl`
+ `#replies` Comment's replies. Returns array of `ReplComment`s

## Language
+ `#id` Language's id (like 'python3' or 'html')
+ `#key` Language's key
+ `#name` Language's name (like 'Python' or 'HTML, CSS, JS')
+ `#tagline` Language's tagline
+ `#category` Category that the language is in
+ `#icon` URL of the language's icon

## Subscription
+ `#id` Ssubscription id
+ `#plan_id` Id of the purchased plan
+ `#quantity` Quantity of the purchase
+ `#timestamp` When the plan was purchased

## Organization
+ `#id` Organization's id
+ `#name` Organization's name
+ `#country` Country where the organization is based
+ `#state` State where the organization is based
+ `#city` City where the organization is based
+ `#postal_code` Organization's postal code
+ `#timestamp` When the organization was created

## Role
+ `#name` Role's name
+ `#key` Role's key
+ `#tagline` Role's tagline

## Board
+ `#id` Board's id
+ `#name` Board's name
+ `#color` Board's color
+ `#description` Borad's description
