---
title: 获取steam中csgo的历史订单
category : Steam
tags : Steam
stickie: false
seo_keywords: 获取steam中csgo的历史订单
seo_description: 获取steam中csgo的历史订单
---

```js
//用steam的购买日期和饰品名称 去和 buff匹配，但可能会出现：同一天，同一件饰品，购买的价格不同，但这种情况购买金额相差肯定不会太大，所有忽略这种情况
const axios = require("axios");
const jsdom = require("jsdom");
const { JSDOM } = jsdom;
let tableDom = null;
let g_rgAssets = null;
let goodsList = [];
let market_name = "";
const CreateItemHoverFromContainer = (
  nouse,
  domID,
  gameID,
  classID,
  goodID,
  t
) => {
  let c = tableDom.getElementById(domID);
  let market_price = c.parentNode.parentNode
    .querySelector(".market_listing_their_price")
    .textContent.trim();

  let market_trade_date = c.parentNode.parentNode
    .querySelectorAll(".market_listing_listed_date")[1]
    .textContent.trim();
  let t_date =
    new Date().getFullYear() +
    "-" +
    market_trade_date
      .split("")
      .filter((e) => e.trim())
      .join("")
      .replace("月", "-")
      .replace("日", "");
  let good = goodsList[goodID];

  return {
    date: t_date,
    market_name: good.market_name,
    market_hash_name: good.market_hash_name,
  };
};
axios
  .get(
    "https://www.fastmock.site/mock/62721202b8757fecaf82bed42dd31502/steam/orders"
  )
  .then((response) => {
    goodsList = response.data.assets[730][2];
    const html = response.data.results_html;
    const jsArr = response.data.hovers
      .replaceAll("\n", "")
      .replaceAll("\t", "")
      .split("\r");

    tableDom = new JSDOM(html).window.document;
    let goods = [];
    jsArr.forEach((e) => {
      if (e.indexOf("_name") != -1) {
        goods.push(eval(e));
      }
    });
    console.log("dd", goods);
  })
  .catch((error) => {
    console.error(error);
  });


```
