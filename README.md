
<!-- README.md is generated from README.Rmd. Please edit that file -->

# atr <img src="man/figures/logo.png" align="right" height="120">

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![R-CMD-check](https://github.com/JBGruber/atr/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/JBGruber/atr/actions/workflows/R-CMD-check.yaml)
[![Codecov test
coverage](https://codecov.io/gh/JBGruber/atr/branch/main/graph/badge.svg)](https://app.codecov.io/gh/JBGruber/atr?branch=main)
<!-- badges: end -->

The goal of atr is to wrap the AT Protocol (Authenticated Transfer
Protocol) behind Bluesky. And we have actually already fulfilled this
goal! The entire protocol is open and documented in so-called
[lexicons](https://atproto.com/guides/lexicon), from which we
autogenerated `R` functions. These are not exported, however, since
dealing with them is a bit advanced. Rather we have some nice
human-generated functions with documentation and examples. Start with
the [Basic Usage](#basic-usage) section below to learn more.

## Installation

You can install the development version of atr like so (`pak` works
similar to `remotes` here, but it’s what the cool kids use now :grin:):

``` r
# install.packages("pak")
pak::pak("JBGruber/atr")
```

# Basic Usage

# Introduction

Welcome to the `atr` vignette! This guide will delve deeper into the
functionalities offered by the package-

Before diving in, let’s clarify some terminology. In the context of Blue
Sky, a post is referred to as a “skeet.” So, when we mention retrieving
or working with “skeets,” we are talking about individual posts made on
Blue Sky.

Now, let’s delve deeper into the functionalities that allow users to
interact with skeets, followers, and their descriptions.

## Installation

Let’s install the package first:

You can install the development version of atr like so (`pak` works
similar to `remotes` here, but it’s what the cool kids use now):

``` r
# install.packages("pak")
pak::pak("JBGruber/atr")
```

## Load the package

``` r
library(atr)
```

## Authentication

The first time you make a request, you will be prompted automatically to
enter your user handle and an app password to authenticate `atr` to
communicate with BlueSky for you.

<figure>
<img src="vignettes/figures/password_popup.png" alt="RStudio Popup" />
<figcaption aria-hidden="true">RStudio Popup</figcaption>
</figure>

The page to generate app passwords is also automatically opened for you.

<figure>
<img src="vignettes/figures/app_password.png"
alt="page to create new app passwords" />
<figcaption aria-hidden="true">page to create new app
passwords</figcaption>
</figure>

However, you can also trigger this process manually:

``` r
auth("jbgruber.bsky.social")
```

This can be useful if you want to replace an old token as it is
permanently stored encrypted on disk.

# Searching Posts

The AT protocol defines a search function at
`app.bsky.feed.searchPosts`, however, it is not currently active, as far
as we could tell.

As soon as the functionality is added by BlueSky, we will implement it
here.

# Blue Sky Users

## Retrieve user info (`get_user_info`)

A good starting point is understanding a user’s digital footprint on the
platform. The get_user_info function becomes our handy tool, fetching an
array of information about a user. All you need? Their handle!

``` r
get_user_info(actor = "atpr.bsky.social")  |>
  dplyr::glimpse()
#> Rows: 1
#> Columns: 8
#> $ did              <chr> "did:plc:j42kj4qc5vfz344weywpkair"
#> $ handle           <chr> "atpr.bsky.social"
#> $ followsCount     <int> 2
#> $ followersCount   <int> 4
#> $ postsCount       <int> 8
#> $ viewer_muted     <lgl> FALSE
#> $ viewer_blockedBy <lgl> FALSE
#> $ viewer_following <chr> "at://did:plc:ntd53albt5ffa4rgervvgibd/ap…
```

This function gives us counts of followers, follows, posts for each
user.

You can also retrieve information for multiple users by providing a
vector of user handles:

``` r

get_user_info(actor = c("benguinaudeau.bsky.social", "atpr.bsky.social"))  |>
  dplyr::glimpse()
#> Rows: 2
#> Columns: 14
#> $ did               <chr> "did:plc:vuvsifrusnjsys7mhkpk662u", "did…
#> $ handle            <chr> "benguinaudeau.bsky.social", "atpr.bsky.…
#> $ displayName       <chr> "Benjamin Guinaudeau", NA
#> $ description       <chr> "Postdoc @CSMaP_NYU. \n\nPolitics, data …
#> $ avatar            <chr> "https://cdn.bsky.app/img/avatar/plain/d…
#> $ banner            <chr> "https://cdn.bsky.app/img/banner/plain/d…
#> $ followsCount      <int> 134, 2
#> $ followersCount    <int> 330, 4
#> $ postsCount        <int> 18, 8
#> $ indexedAt         <chr> "2023-09-19T00:08:33.661Z", NA
#> $ viewer_muted      <lgl> FALSE, FALSE
#> $ viewer_blockedBy  <lgl> FALSE, FALSE
#> $ viewer_following  <chr> "at://did:plc:ntd53albt5ffa4rgervvgibd/a…
#> $ viewer_followedBy <chr> "at://did:plc:vuvsifrusnjsys7mhkpk662u/a…
```

## Retrieve Skeets (`get_skeets_authored_by`)

To fetch all the skeets by a specific user, use the
`get_skeets_authored_by` function. *Note this also includes quote skeets
and reskeets.* You can also opt not to parse the result by setting
`parse = FALSE`, however it is recommended to use the default parse
option which results in a (more) tidy tibble.

``` r
 get_skeets_authored_by(actor = "benguinaudeau.bsky.social", parse = TRUE) |>
  dplyr::glimpse()
#> Rows: 24
#> Columns: 11
#> $ uri          <chr> "at://did:plc:l6z5l7dtmoalsxl6v6a57mjk/app.bs…
#> $ cid          <chr> "bafyreihfmnxonlhlukswv6flaf4xzporosdbwo57ii3…
#> $ author       <list> ["did:plc:l6z5l7dtmoalsxl6v6a57mjk", "elisad…
#> $ text         <chr> "I am very much looking forward to the CPPE t…
#> $ record       <list> ["I am very much looking forward to the CPPE…
#> $ reply_count  <int> 0, 3, 0, 2, 0, 2, 0, 0, 1, 0, 1, 1, 0, 1, 1, …
#> $ repost_count <int> 2, 45, 0, 12, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0…
#> $ like_count   <int> 8, 38, 2, 36, 1, 5, 4, 1, 0, 4, 4, 3, 0, 1, 0…
#> $ indexed_at   <dttm> 2023-10-05 15:53:49, 2023-10-04 13:15:35, 20…
#> $ reply        <list> <NULL>, <NULL>, [["app.bsky.feed.defs#postVi…
#> $ is_reskeet   <lgl> TRUE, TRUE, FALSE, TRUE, FALSE, FALSE, FALSE,…
```

Feeling adventurous? Increase the `limit` and explore more of their
content.

``` r
get_skeets_authored_by(actor = "favstats.bsky.social", limit = 30) |>
  dplyr::glimpse()
#> Rows: 30
#> Columns: 11
#> $ uri          <chr> "at://did:plc:7ao77wemnhl63qnpuy7psebr/app.bs…
#> $ cid          <chr> "bafyreih2ave4w472kho4wzhdfe66pvfg7gaecg4s2on…
#> $ author       <list> ["did:plc:7ao77wemnhl63qnpuy7psebr", "whotar…
#> $ text         <chr> "Hallo to all our new followers from Germany!…
#> $ record       <list> ["Hallo to all our new followers from German…
#> $ reply_count  <int> 0, 0, 6, 0, 1, 1, 1, 3, 0, 1, 1, 0, 3, 0, 1, …
#> $ repost_count <int> 1, 3, 25, 0, 0, 0, 28, 10, 0, 0, 3, 0, 0, 1, …
#> $ like_count   <int> 3, 7, 50, 3, 2, 0, 62, 32, 1, 1, 12, 1, 4, 3,…
#> $ indexed_at   <dttm> 2023-10-06 08:28:06, 2023-10-05 14:41:15, 20…
#> $ reply        <list> <NULL>, <NULL>, <NULL>, [["app.bsky.feed.def…
#> $ is_reskeet   <lgl> TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, TRUE, …
```

# Exploring Connections: Get Followers and Follows

You can retrieve a list of followers and those followed by a user
through the `get_followers` and `get_follows` functions, respectively.
This gives a glimpse of their social dynamics on the platform.

## `get_followers`

``` r
get_followers(actor = "benguinaudeau.bsky.social", limit = 200)  |>
  dplyr::glimpse()
#> Rows: 204
#> Columns: 11
#> $ did                <chr> "did:plc:wga4zwww2ddx6igumcdyp2nu", "di…
#> $ handle             <chr> "mbayerlein.bsky.social", "mgmacdonald.…
#> $ display_name       <chr> "Michael Bayerlein", "Maggie Macdonald,…
#> $ description        <chr> "PostDoc at SWP Berlin || EU, Health, P…
#> $ avatar             <chr> "https://cdn.bsky.app/img/avatar/plain/…
#> $ indexed_at         <chr> "2023-10-11T07:31:21.023Z", "2023-09-26…
#> $ viewer_muted       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALS…
#> $ viewer_blocked_by  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALS…
#> $ viewer_following   <chr> "at://did:plc:ntd53albt5ffa4rgervvgibd/…
#> $ viewer_followed_by <chr> "at://did:plc:wga4zwww2ddx6igumcdyp2nu/…
#> $ labels             <named list> <NULL>, <NULL>, <NULL>, <NULL>, …
```

## `get_follows`

``` r
atr:::get_follows(actor = "benguinaudeau.bsky.social", limit = 200)  |>
  dplyr::glimpse()
#> Rows: 152
#> Columns: 11
#> $ did                  <chr> "did:plc:zxuic5k6po2kaxrkzb3c5uoc", "…
#> $ handle               <chr> "marcdebus.bsky.social", "chrisbail.b…
#> $ display_name         <chr> "Marc Debus", "Chris Bail", NA, "Jan …
#> $ description          <chr> "Professor of Political Science at th…
#> $ avatar               <chr> "https://cdn.bsky.app/img/avatar/plai…
#> $ indexed_at           <chr> "2023-10-04T08:18:44.258Z", "2023-10-…
#> $ viewer_muted         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FA…
#> $ viewer_blocked_by    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FA…
#> $ viewer_following     <chr> NA, NA, "at://did:plc:ntd53albt5ffa4r…
#> $ viewer_followed_by   <chr> NA, NA, NA, "at://did:plc:5ax6yywsizs…
#> $ viewer_muted_by_list <named list> <NULL>, <NULL>, <NULL>, <NULL>…
```

You’ll notice each follower/following has a description (i.e. their
bio). It’s a nifty way to get to know a bit about them at a glance!

## Follower Network Visualization

This section guides you through the process of visualizing a follower
network for a given user on Blue Sky. The primary focus is on the
“skeets” (posts) and the interconnected relationships among followers.

The first step is to load some additional packages. Then we get the
followers for the main user we are interested in. We will then delve
deeper and fetch the followers for each of these followers.

``` r
library(ggplot2)
library(dplyr)
library(purrr)
library(tidyr)
library(igraph)
library(ggraph)
library(tidygraph)

# Retrieve the followers for the main user
some_followers <- get_followers(actor = "benguinaudeau.bsky.social", limit = 10)$handle

# For each follower, retrieve their own set of followers.
# This provides a nested view of relationships.
followers_of_followers <- some_followers |>
  purrr::map_dfr(~{
    get_followers(actor = .x, limit = 200) |>
    mutate(from = .x)
  }) %>%
  dplyr::rename(to = handle) %>%
  dplyr::select(from, to)
```

## Building and Visualizing the Follower Network

``` r

# Construct the network graph and plot
graph <- igraph::graph_from_data_frame(followers_of_followers, directed = TRUE)

# Use ggraph to visualize the network.
ggraph::ggraph(graph,  layout = 'kk') +
  ggraph::geom_edge_link(width = 0.2) +
  ggraph::geom_node_point(aes(size = tidygraph::centrality_pagerank())) +
  ggplot2::theme_void()
```

<figure>
<img src="vignettes/figures/unnamed-chunk-10-1.png"
alt="plot of chunk unnamed-chunk-10" />
<figcaption aria-hidden="true">plot of chunk
unnamed-chunk-10</figcaption>
</figure>

# Interacting with Blue Sky: Posts, Replies, and Deletions

In this section, we will cover how to interact with the Blue Sky
platform by creating posts (skeets), replying to them, and eventually,
if necessary, deleting them.

## Posting a Skeet

Here’s how you can easily post a skeet to your account:

``` r

post <- post_skeet(text = "Posting around")
```

By running the above command, you’ve successfully created a skeet with
the content “Posting around.”

## Replying to a Skeet

Engaging with the Blue Sky community often means replying to existing
skeets. Here’s how you can craft a reply:

``` r

reply <- post_skeet(text = "Replying around", in_reply_to = post$uri)
```

Notice the in_reply_to parameter? This ensures that our reply is linked
to the original post, creating a thread of conversation.

## Deleting a Skeet

Whether it’s to correct an error, remove outdated information, or any
other reason, sometimes you might want to delete a skeet. Here’s how:

``` r
delete_skeet(post_url = reply$uri)
```

## Crafting Threads in Blue Sky

Threads are a series of connected posts or “skeets” on Blue Sky. They’re
a great way to communicate longer ideas or stories, where each skeet
builds on the previous one. In this section, we’ll explore how to create
a thread seamlessly.

A thread is essentially a series of individual skeets. Here’s how you
can do it programmatically using `post_thread`:

``` r
post_thread(texts = c("This is the first amazing skeet.",
                      "Now I am expanding upon my skeet!",
                      "Conclusion: I skeet therefore I am."))
```

## Following and Unfollowing Users in Blue Sky

One of the fundamental ways to connect with other users is by following
them, allowing you to see their posts in your feed. Conversely, if you
decide that you no longer want to see updates from a particular user,
you can unfollow them.

``` r
# Follow a user by their handle
follow(actor = "benguinaudeau.bsky.social")
```

``` r
# Follow a user by their handle
follow(actor = "benguinaudeau.bsky.social")
```

<!-- ## Support for posting media -->
<!-- ```{r, eval=F} -->
<!-- ## Not working -->
<!-- library(ggplot2) -->
<!-- ggplot(mpg, aes(displ, hwy, colour = class)) +  -->
<!--   geom_point() -->
<!-- f1 <- ggsave(tempfile(pattern = "1", fileext = ".png")) -->
<!-- lty <- c("solid", "dashed", "dotted", "dotdash", "longdash", "twodash") -->
<!-- linetypes <- data.frame( -->
<!--   y = seq_along(lty), -->
<!--   lty = lty -->
<!-- )  -->
<!-- ggplot(linetypes, aes(0, y)) +  -->
<!--   geom_segment(aes(xend = 5, yend = y, linetype = lty)) +  -->
<!--   scale_linetype_identity() +  -->
<!--   geom_text(aes(label = lty), hjust = 0, nudge_y = 0.2) + -->
<!--   scale_x_continuous(NULL, breaks = NULL) +  -->
<!--   scale_y_reverse(NULL, breaks = NULL) -->
<!-- f2 <- ggsave(tempfile(pattern = "2", fileext = ".png")) -->
<!-- post_thread(texts = c("This is the first plot on the ggplot2 website", -->
<!--                       "This is the second plot on the ggplot2 website", -->
<!--                       "And this is just text sent from the atr package by @jbgruber.bsky.social, @benguinaudeau.bsky.social and @favstats.bsky.social"),  -->
<!--             images = c(f1, f2, ""), -->
<!--             image_alts = c("first ggplot2 plot", -->
<!--                            "second ggplot2 plot", -->
<!--                            "")) -->
<!-- thread <- post_thread(c("Post 1", "Post 2", "Post 3")) -->
<!-- delete_post(thread$uri) -->
<!-- ``` -->

# Analyzing Feeds on Blue Sky

On Blue Sky users have the ability to create custom feeds based on
specific keywords. These feeds aggregate content, for instance, a user
might curate a feed around the hashtag `#rstats` to gather all relevant
content about. Let’s delve into the dynamics of such user-curated feeds.

## Retrieving a Curated Feed

Our starting point is to extract the posts from a user-curated feed.
We’re focusing on a feed curated by “andrew.heiss.phd”.

``` r
# Fetching the feed posts
feeds <- get_feeds_created_by(actor = "andrew.heiss.phd") |>
  dplyr::glimpse()
#> Rows: 4
#> Columns: 19
#> $ uri                      <chr> "at://did:plc:2zcfjzyocp6kapg6jc4…
#> $ cid                      <chr> "bafyreicvjczzxxhrkrl4c2xvfu7yhnm…
#> $ did                      <chr> "did:web:skyfeed.me", "did:web:sk…
#> $ creator_did              <chr> "did:plc:2zcfjzyocp6kapg6jc4eacok…
#> $ creator_handle           <chr> "andrew.heiss.phd", "andrew.heiss…
#> $ creator_displayName      <chr> "Andrew Heiss, but ~🎃spoOOoky👻~…
#> $ creator_description      <chr> "Assistant professor at Georgia S…
#> $ creator_avatar           <chr> "https://cdn.bsky.app/img/avatar/…
#> $ creator_indexedAt        <chr> "2023-10-01T13:25:49.025Z", "2023…
#> $ creator_viewer_muted     <lgl> FALSE, FALSE, FALSE, FALSE
#> $ creator_viewer_blockedBy <lgl> FALSE, FALSE, FALSE, FALSE
#> $ creator_viewer_following <chr> "at://did:plc:ntd53albt5ffa4rgerv…
#> $ displayName              <chr> "Public Admin/Policy", "Nonprofit…
#> $ description              <chr> "A feed for public administration…
#> $ avatar                   <chr> "https://cdn.bsky.app/img/avatar/…
#> $ likeCount                <int> 72, 15, 0, 73
#> $ indexedAt                <chr> "2023-09-21T01:37:55.774Z", "2023…
#> $ created_at               <dttm> 2023-09-21 01:37:55, 2023-09-21 0…
#> $ viewer_like              <chr> NA, NA, NA, "at://did:plc:ntd53al…

# Filtering for a specific keyword, for example "#rstats"
rstat_feed <- feeds |>
  filter(displayName == "#rstats")

# Extracting posts from this curated feed
rstat_posts <- get_feed(rstat_feed$uri, limit = 200) |>
  # Extracting user handle from the author
  mutate(handle = author |> map_chr(~{.x$handle}))  |>
  dplyr::glimpse()
#> Rows: 90
#> Columns: 11
#> $ uri          <chr> "at://did:plc:bpwmgq35v7snpvi6xxjhgucy/app.bs…
#> $ cid          <chr> "bafyreic7iiamtnin5oluodqsrq4ivuh5kgiapnsvug2…
#> $ author       <list> ["did:plc:bpwmgq35v7snpvi6xxjhgucy", "fdecht…
#> $ text         <chr> "Hi Bluesky! I finally made it to the other s…
#> $ record       <list> ["Hi Bluesky! I finally made it to the other…
#> $ reply_count  <int> 0, 1, 1, 0, 0, 1, 1, 0, 3, 2, 0, 0, 1, 2, 0, …
#> $ repost_count <int> 1, 1, 1, 2, 0, 0, 0, 0, 0, 1, 0, 1, 0, 0, 1, …
#> $ like_count   <int> 4, 6, 7, 5, 1, 1, 5, 0, 9, 1, 1, 3, 6, 4, 5, …
#> $ indexed_at   <dttm> 2023-10-11 06:18:40, 2023-10-11 00:53:13, 20…
#> $ reply        <list> <NULL>, <NULL>, <NULL>, <NULL>, <NULL>, <NUL…
#> $ handle       <chr> "fdechterenko.bsky.social", "ceenell.bsky.soc…
```

## Identifying Top Contributors

Who are the leading voices within a particular topic? This analysis
highlights users who are frequently contributing to the `#rstats` feed.

``` r
library(ggplot2)

# Identifying the top 10 contributors
rstat_posts |>
  count(handle, sort = T) %>%
  slice(1:10) %>%
  mutate(handle = forcats::fct_reorder(handle, n)) %>%
  ggplot(aes(handle, n)) +
  geom_col() +
  coord_flip()
```

<figure>
<img src="vignettes/figures/unnamed-chunk-18-1.png"
alt="plot of chunk unnamed-chunk-18" />
<figcaption aria-hidden="true">plot of chunk
unnamed-chunk-18</figcaption>
</figure>

### Recognizing Influential Voices

Volume doesn’t always translate to influence. Some users may post less
frequently but their contributions resonate deeply with the community.

``` r
# Identifying top 10 influential voices based on likes
rstat_posts |>
  group_by(handle) |>
  summarize(like_count = sum(like_count)) |>
  ungroup() |>
  arrange(desc(like_count)) |>
  slice(1:10) |>
  mutate(handle = forcats::fct_reorder(handle, like_count)) |>
  ggplot(aes(handle, like_count)) +
  geom_col() +
  coord_flip()
```

<figure>
<img src="vignettes/figures/unnamed-chunk-19-1.png"
alt="plot of chunk unnamed-chunk-19" />
<figcaption aria-hidden="true">plot of chunk
unnamed-chunk-19</figcaption>
</figure>

### Most Famous \#rstats skeet

``` r

# Finding the standout post in the rstats feed
rstat_posts |>
  mutate(total_interactions = reply_count+repost_count+like_count) |>
  arrange(desc(total_interactions)) |>
  slice(1) |>
  select(handle, total_interactions, text) |>
  dplyr::glimpse() |>
  pull(text)
#> Rows: 1
#> Columns: 3
#> $ handle             <chr> "andrew.heiss.phd"
#> $ total_interactions <int> 234
#> $ text               <chr> "New #rstats color palette package just…
#> [1] "New #rstats color palette package just dropped: {MoMAColors}! It has gorgeous colors from art at the Museum of Modern Art in NYC"
```

<!-- ## Get likes from a post -->
<!-- ### Like analysis -->
<!-- ```{r} -->
<!-- uri <- convert_http_to_at("https://bsky.app/profile/ryanlcooper.com/post/3kb42gayda52t") -->
<!-- # Not working for now -->
<!-- get_likes(uri) -->
<!-- ``` -->
<!-- ### Repost analysis -->
<!-- ```{r} -->
<!-- uri <- convert_http_to_at("https://bsky.app/profile/ryanlcooper.com/post/3kb42gayda52t") -->
<!-- # Not working for now -->
<!-- get_reposts(post_url = uri) -->
<!-- ``` -->
<!-- ### Eventual temporal structure -->

# Want to help?

You can help by creating an
[issue](https://github.com/JBGruber/atr/issues/new/choose) requesting
new features or reporting bugs. If you are a developer, we are happy to
accept pull requests. It should be fairly straightforward, as all
endpoints are already covered by automatically generated function. But
please open an issue first, so we don’t do duplicated work.
