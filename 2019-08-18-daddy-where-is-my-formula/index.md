# Daddy, where is my formula?


Chase is only 20 months old, but he eats a lot... Well, he got a fat father, who can I blame...  - -|||

<!--more-->

Baby formula is always a hard thing to get in Australia as the producer is not able to supply enough to all Asian countries... (No blame here...)

Not many ways that we can get the formula:

- Chinese Daigou shop
  - \$37 per can, average \$7 more than retail price
- Supermarket (Coles, WWS, etc...)
  - Always out of stock
- Online (Mum's store)
  - 1 can per order with \$10 postage fee = \$30 + \$10 even more expensive than Daigou shop

So do it in golang with basic operations... 

1. Find API for checking the stock level
2. Call API to get result
3. Sort JSON data
4. Operate with data

# Find API

Web page: https://www.woolworths.com.au/shop/productdetails/7211/aptamil-profutura-toddler-formula-stage-3

By clicking the `Check stock in our stores` to find out if it's purchasable.

With Chrome Dev Tool, the API url can be found out here:
https://www.woolworths.com.au/apis/ui/product/7211/Stores?IncludeInStockStoreOnly=false&Latitude=-33.7038507&Longitude=151.1087877&Max=5

- In url `7211`: product id
- IncludeInStockStoreOnly: limited result
- Latitude & Longitude: Location
- Max: limited result

# Call API

Call API with golang by using `http.Get`:

```golang
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
)

func main() {
	// get stock status for item 7211 around Hornsby area from API call: https://www.woolworths.com.au/apis/ui/product/7211/Stores?IncludeInStockStoreOnly=false&Latitude=-33.7038507&Longitude=151.1087877&Max=10
	// Latitude & Longitude = Hornsby
	// 7211 = Atapmil pro-futura Toddler Formula Stage 3 900g
	// Max = maximum number of stores information as return

	resp, err := http.Get("https://www.woolworths.com.au/apis/ui/product/7211/Stores?IncludeInStockStoreOnly=false&Latitude=-33.7038507&Longitude=151.1087877&Max=10")
	if err != nil {
		panic(err)
	}

	defer resp.Body.Close()

	body, err := ioutil.ReadAll(resp.Body)

	fmt.Println(string(body))
}
```

Result:

```json
[
  {
    "Store":{
      "Division":"SUPERMARKETS",
      "StoreNo":"1294",
      "Name":"Hornsby",
      "AddressLine1":"Westfield Shopping Centre, 236 Pacific Highway",
      "AddressLine2":null,
      "Suburb":"Hornsby",
      "State":"NSW",
      "Postcode":"2077",
      "Latitude":"-33.704725",
      "Longitude":"151.099336",
      "GeoLevel":"0",
      "Distance":"0.9",
      "Phone":"(02) 9450 6712",
      "TradingHours":[
        {
          "Day":"Today",
          "OpenHour":"6:00 AM - 10:00 PM",
          "TradingHourForDisplay":"Mon: 0600-2200"
        },
        {
          "Day":"Tomorrow",
          "OpenHour":"6:00 AM - 10:00 PM",
          "TradingHourForDisplay":"Tue: 0600-2200"
        },
        {
          "Day":"Wednesday",
          "OpenHour":"6:00 AM - 10:00 PM",
          "TradingHourForDisplay":"Wed: 0600-2200"
        },
        {
          "Day":"Thursday",
          "OpenHour":"6:00 AM - 10:00 PM",
          "TradingHourForDisplay":"Thu: 0600-2200"
        },
        {
          "Day":"Friday",
          "OpenHour":"6:00 AM - 10:00 PM",
          "TradingHourForDisplay":"Fri: 0600-2200"
        },
        {
          "Day":"Saturday",
          "OpenHour":"6:00 AM - 10:00 PM",
          "TradingHourForDisplay":"Sat: 0600-2200"
        },
        {
          "Day":"Sunday",
          "OpenHour":"6:00 AM - 10:00 PM",
          "TradingHourForDisplay":"Sun: 0600-2200"
        },
        {
          "Day":"Monday",
          "OpenHour":"6:00 AM - 10:00 PM",
          "TradingHourForDisplay":"Mon: 0600-2200"
        }
      ],
      "Facilities":[
        "Coffee Shop",
        "Seafood",
        "Sushi Bar",
        "Pick up",
        "eBay Online Order Collection",
        "EziBuy Online Order Collection",
        "Click and Collect"
      ]
    },
    "ListPrice":0,
    "InstoreListPrice":0,
    "SalePrice":0,
    "InstoreSalePrice":0,
    "CUPListPrice":0,
    "InstoreCUPListPrice":0,
    "CUPSalePrice":0,
    "InstoreCUPSalePrice":0,
    "HideWasSavedPrice":false,
    "IsRanged":true,
    "IsInStock":false,
    "IsForCollection":false,
    "IsForDelivery":false,
    "IsForExpress":false,
    "IsSpecial":false,
    "IsAvailable":false,
    "InstoreIsAvailable":false,
    "IsPurchasable":false,
    "InstoreIsPurchasable":false,
    "IsOnSpecial":false,
    "InstoreIsOnSpecial":false,
    "IsNew":false
  }
]
```

# Analysis Data

To analysis JSON data, we need to declare the data structure based on JSON result first:

```golang
type product_store_level []struct {
	Store struct {
		Division     string      `json:"Division"`
		StoreNo      string      `json:"StoreNo"`
		Name         string      `json:"Name"`
		AddressLine1 string      `json:"AddressLine1"`
		AddressLine2 interface{} `json:"AddressLine2"`
		Suburb       string      `json:"Suburb"`
		State        string      `json:"State"`
		Postcode     string      `json:"Postcode"`
		Latitude     string      `json:"Latitude"`
		Longitude    string      `json:"Longitude"`
		GeoLevel     string      `json:"GeoLevel"`
		Distance     string      `json:"Distance"`
		Phone        string      `json:"Phone"`
		TradingHours []struct {
			Day                   string `json:"Day"`
			OpenHour              string `json:"OpenHour"`
			TradingHourForDisplay string `json:"TradingHourForDisplay"`
		} `json:"TradingHours"`
		Facilities []string `json:"Facilities"`
	} `json:"Store"`
	ListPrice            int     `json:"ListPrice"`
	InstoreListPrice     int     `json:"InstoreListPrice"`
	SalePrice            int     `json:"SalePrice"`
	InstoreSalePrice     int     `json:"InstoreSalePrice"`
	CUPListPrice         float64 `json:"CUPListPrice"`
	InstoreCUPListPrice  float64 `json:"InstoreCUPListPrice"`
	CUPSalePrice         float64 `json:"CUPSalePrice"`
	InstoreCUPSalePrice  float64 `json:"InstoreCUPSalePrice"`
	HideWasSavedPrice    bool    `json:"HideWasSavedPrice"`
	IsRanged             bool    `json:"IsRanged"`
	IsInStock            bool    `json:"IsInStock"`
	IsForCollection      bool    `json:"IsForCollection"`
	IsForDelivery        bool    `json:"IsForDelivery"`
	IsForExpress         bool    `json:"IsForExpress"`
	IsSpecial            bool    `json:"IsSpecial"`
	IsAvailable          bool    `json:"IsAvailable"`
	InstoreIsAvailable   bool    `json:"InstoreIsAvailable"`
	IsPurchasable        bool    `json:"IsPurchasable"`
	InstoreIsPurchasable bool    `json:"InstoreIsPurchasable"`
	IsOnSpecial          bool    `json:"IsOnSpecial"`
	InstoreIsOnSpecial   bool    `json:"InstoreIsOnSpecial"`
	IsNew                bool    `json:"IsNew"`
}
```

Then use json encoder to help analysis data:

```golang
var r product_store_level

err = json.Unmarshal(body, &r)
if err != nil {
    panic(err)
}

for _, i := range r[0:] {
    if i.IsPurchasable {
        fmt.Println(i.Store.Suburb, "有货!")
    }
}
```

---

Inside the `if` part, we can simply put more actions: like `email`, `sms`, etc... to notify users.


Anyway, by using AWS lambda + SNS service, we are able to get the notification easily... 

It's a good start with golang from useful project, family side... ~ at least Chase got formula... 
