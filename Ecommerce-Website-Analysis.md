# THE OBJECTIVE

Use SQL to:
Extract and analyze traffic and website performance data to craft a growth story that your
CEO can sell. Dive into the marketing channel activities and the website improvements that
have contributed to your success to date, and use the opportunity to flex your analytical skills
for the investors while you’re at it.
As an Analyst, the first part of your job is extracting and analyzing the data. The next (equally
important) part is communicating the story effectively to your stakeholders.

![Tables-Relation](Tables-Relation.png)

## Task 1: Volume Growth Analysis

- [ ] Pull overall session and order volume trended by quarter for the life of the business.
- [ ] Decide on how to handle the most recent incomplete quarter.

```
SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
	COUNT(DISTINCT website_sessions.website_session_id) AS sessions, 
    COUNT(DISTINCT orders.order_id) AS orders
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2
;
```
| yr   | qtr | sessions | orders |
|------|---------|----------|--------|
| 2012 | 1       | 1879     | 60     |
| 2012 | 2       | 11433    | 347    |
| 2012 | 3       | 16892    | 684    |
| 2012 | 4       | 32266    | 1495   |
| 2013 | 1       | 19833    | 1273   |
| 2013 | 2       | 24745    | 1718   |
| 2013 | 3       | 27663    | 1840   |
| 2013 | 4       | 40540    | 2616   |
| 2014 | 1       | 46779    | 3069   |
| 2014 | 2       | 53129    | 3848   |
| 2014 | 3       | 57141    | 4035   |
| 2014 | 4       | 76373    | 5908   |
| 2015 | 1       | 64198    | 5420   |

Our business is on the rise! The latest numbers, even though the last quarter is incomplete, show strong growth in both sessions and order volumes. Things are looking up! 
For the last incomplete quarter, we've opted to exclude it from our analysis to ensure a consistent and reliable trend across complete data points.
## Task 2: Showcase Efficiency Improvements

- [ ] Showcase quarterly figures, since the launch, for session-to-order conversion rate.
- [ ] Showcase quarterly figures, since the launch, for revenue per order.
- [ ] Showcase quarterly figures, since the launch, for revenue per session.
```
SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
	COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT website_sessions.website_session_id) AS session_to_order_conv_rate, 
    SUM(price_usd)/COUNT(DISTINCT orders.order_id) AS revenue_per_order, 
    SUM(price_usd)/COUNT(DISTINCT website_sessions.website_session_id) AS revenue_per_session
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2
;
```
| yr   | qtr | sessions_to_order_cov_rate | revenue_per_order | revenue_per_session |
|------|-----|---------------------------|-------------------|----------------------|
| 2012 | 1   | 0.0319                    | 49.990000         | 1.596275             |
| 2012 | 2   | 0.0304                    | 49.990000         | 1.517233             |
| 2012 | 3   | 0.0405                    | 49.990000         | 2.024222             |
| 2012 | 4   | 0.0463                    | 49.990000         | 2.316217             |
| 2013 | 1   | 0.0642                    | 52.142396         | 3.346809             |
| 2013 | 2   | 0.0694                    | 51.538312         | 3.578211             |
| 2013 | 3   | 0.0665                    | 51.734533         | 3.441114             |
| 2013 | 4   | 0.0645                    | 54.715688         | 3.530741             |
| 2014 | 1   | 0.0656                    | 62.160684         | 4.078136             |
| 2014 | 2   | 0.0724                    | 64.374207         | 4.662462             |
| 2014 | 3   | 0.0706                    | 64.494949         | 4.554298             |
| 2014 | 4   | 0.0774                    | 63.793497         | 4.934885             |
| 2015 | 1   | 0.0844                    | 62.799917         | 5.301965             |

The session-to-order conversion rate surged from 3% to approximately 8% in the latest quarter, showcasing a substantial improvement in website engagement. Concurrently, the revenue per order witnessed significant growth, and the revenue per session skyrocketed from $1.59 to over $5, indicating a remarkable increase in the value derived from each customer interaction.

## Task 3: Growth Analysis of Specific Channels

- [ ] Pull quarterly orders from Gsearch nonbrand, Bsearch nonbrand, brand search overall, organic search, and direct type-in.
```
SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
    COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) AS gsearch_nonbrand_orders, 
    COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) AS bsearch_nonbrand_orders, 
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN orders.order_id ELSE NULL END) AS brand_search_orders,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN orders.order_id ELSE NULL END) AS organic_search_orders,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN orders.order_id ELSE NULL END) AS direct_type_in_orders
    
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2
```
| yr   | qtr | gsearch_nonbrand_orders | bsearch_nonbrand_orders | brand_search_orders | organic_search_orders | direct_type_in_orders |
|------|-----|-------------------------|-------------------------|----------------------|------------------------|------------------------|
| 2012 | 1   | 60                      | 0                       | 0                    | 0                      | 0                      |
| 2012 | 2   | 291                     | 0                       | 20                   | 15                     | 21                     |
| 2012 | 3   | 482                     | 82                      | 48                   | 40                     | 32                     |
| 2012 | 4   | 913                     | 311                     | 88                   | 94                     | 89                     |
| 2013 | 1   | 766                     | 183                     | 108                  | 125                    | 91                     |
| 2013 | 2   | 1114                    | 237                     | 114                  | 134                    | 119                    |
| 2013 | 3   | 1132                    | 245                     | 153                  | 167                    | 143                    |
| 2013 | 4   | 1657                    | 291                     | 248                  | 223                    | 197                    |
| 2014 | 1   | 1667                    | 344                     | 354                  | 338                    | 311                    |
| 2014 | 2   | 2208                    | 427                     | 410                  | 436                    | 367                    |
| 2014 | 3   | 2259                    | 434                     | 432                  | 445                    | 402                    |
| 2014 | 4   | 3248                    | 683                     | 615                  | 605                    | 532                    |
| 2015 | 1   | 3025                    | 581                     | 622                  | 640                    | 552                    |

The investor would be particularly interested in the increase of brand-search-orders, organic-search-orders, and direct-type-in-orders.

## Task 4: Analyze Session-to-Order Conversion Rate Trends

- [ ] Show quarterly trends for session-to-order conversion rates.
- [ ] Break down the analysis for Gsearch nonbrand, Bsearch nonbrand, brand search overall, organic search, and direct type-in.
- [ ] Make note of any periods with significant improvements or optimizations.
```
SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
    COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END)
		/COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' AND utm_campaign = 'nonbrand' THEN website_sessions.website_session_id ELSE NULL END) AS gsearch_nonbrand_conv_rt, 
    COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' AND utm_campaign = 'nonbrand' THEN website_sessions.website_session_id ELSE NULL END) AS bsearch_nonbrand_conv_rt, 
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN website_sessions.website_session_id ELSE NULL END) AS brand_search_conv_rt,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN website_sessions.website_session_id ELSE NULL END) AS organic_search_conv_rt,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN website_sessions.website_session_id ELSE NULL END) AS direct_type_in_conv_rt
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2
;
```
## Task 5: Monthly Revenue and Margin Analysis by Product

- [ ] Pull monthly trends for revenue and margin by product.
- [ ] Include total sales and revenue in the analysis.
- [ ] Make notes regarding any observed patterns or seasonality.
```
SELECT
	YEAR(created_at) AS yr, 
    MONTH(created_at) AS mo, 
    SUM(CASE WHEN product_id = 1 THEN price_usd ELSE NULL END) AS mrfuzzy_rev,
    SUM(CASE WHEN product_id = 1 THEN price_usd - cogs_usd ELSE NULL END) AS mrfuzzy_marg,
    SUM(CASE WHEN product_id = 2 THEN price_usd ELSE NULL END) AS lovebear_rev,
    SUM(CASE WHEN product_id = 2 THEN price_usd - cogs_usd ELSE NULL END) AS lovebear_marg,
    SUM(CASE WHEN product_id = 3 THEN price_usd ELSE NULL END) AS birthdaybear_rev,
    SUM(CASE WHEN product_id = 3 THEN price_usd - cogs_usd ELSE NULL END) AS birthdaybear_marg,
    SUM(CASE WHEN product_id = 4 THEN price_usd ELSE NULL END) AS minibear_rev,
    SUM(CASE WHEN product_id = 4 THEN price_usd - cogs_usd ELSE NULL END) AS minibear_marg,
    SUM(price_usd) AS total_revenue,  
    SUM(price_usd - cogs_usd) AS total_margin
FROM order_items 
GROUP BY 1,2
ORDER BY 1,2
;
```
## Task 6: Analyze Impact of Introducing New Products

- [ ] Pull monthly sessions to the /products page.
- [ ] Show the change in the percentage of those sessions clicking through to another page over time.
- [ ] Provide a view of how the conversion from /products to placing an order has improved.
```
-- first, identifying all the views of the /products page
CREATE TEMPORARY TABLE products_pageviews
SELECT
	website_session_id, 
    website_pageview_id, 
    created_at AS saw_product_page_at

FROM website_pageviews 
WHERE pageview_url = '/products'
;
```
```
SELECT 
	YEAR(saw_product_page_at) AS yr, 
    MONTH(saw_product_page_at) AS mo,
    COUNT(DISTINCT products_pageviews.website_session_id) AS sessions_to_product_page, 
    COUNT(DISTINCT website_pageviews.website_session_id) AS clicked_to_next_page, 
    COUNT(DISTINCT website_pageviews.website_session_id)/COUNT(DISTINCT products_pageviews.website_session_id) AS clickthrough_rt,
    COUNT(DISTINCT orders.order_id) AS orders,
    COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT products_pageviews.website_session_id) AS products_to_order_rt
FROM products_pageviews
	LEFT JOIN website_pageviews 
		ON website_pageviews.website_session_id = products_pageviews.website_session_id -- same session
        AND website_pageviews.website_pageview_id > products_pageviews.website_pageview_id -- they had another page AFTER
	LEFT JOIN orders 
		ON orders.website_session_id = products_pageviews.website_session_id
GROUP BY 1,2
;
```
## Task 7: Analyze Cross-Selling Impact of 4th Product Introduction

- [ ] Pull sales data since December 05, 2014, for the 4th product introduced as a primary product.
- [ ] Evaluate the cross-selling performance of each product to one another.
```
CREATE TEMPORARY TABLE primary_products
SELECT 
	order_id, 
    primary_product_id, 
    created_at AS ordered_at
FROM orders 
WHERE created_at > '2014-12-05' -- when the 4th product was added (says so in question)
;
```
```
SELECT
	primary_products.*, 
    order_items.product_id AS cross_sell_product_id
FROM primary_products
	LEFT JOIN order_items 
		ON order_items.order_id = primary_products.order_id
        AND order_items.is_primary_item = 0; -- only bringing in cross-sells;
```

```

SELECT 
	primary_product_id, 
    COUNT(DISTINCT order_id) AS total_orders, 
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 1 THEN order_id ELSE NULL END) AS _xsold_p1,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 2 THEN order_id ELSE NULL END) AS _xsold_p2,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 3 THEN order_id ELSE NULL END) AS _xsold_p3,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 4 THEN order_id ELSE NULL END) AS _xsold_p4,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 1 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p1_xsell_rt,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 2 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p2_xsell_rt,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 3 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p3_xsell_rt,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 4 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p4_xsell_rt
FROM
(
SELECT
	primary_products.*, 
    order_items.product_id AS cross_sell_product_id
FROM primary_products
	LEFT JOIN order_items 
		ON order_items.order_id = primary_products.order_id
        AND order_items.is_primary_item = 0 -- only bringing in cross-sells
) AS primary_w_cross_sell
GROUP BY 1;
```

