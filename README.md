# Türkiye BIN Listesi (Güncel) / Turkey BIN List (Latest)

TR: Türkiye **güncel BIN listesi**. Ödeme sistemleri, fraud teknolojileri ve benzeri projeler için kullanılabilecek güncel liste.

EN: Turkey **latest BIN list** an up-to-date list that can be used for payment systems, fraud technologies, and similar projects.

## Özellikler / Features
- TR: **Sürekli güncellenen** Türkiye BIN listesi
- EN: **Continuously updated** Turkey BIN list
- TR/EN: Kolay entegrasyon (Easy integration): JSON

## Veri / Data
- `list.json`

> TR: “Güncel / latest” durumunu takip etmek için commit geçmişine bakabilirsiniz.

> EN: To confirm freshness, check commit history if available.

## Kullanım / Usage

### Node.js
```js
import fs from "node:fs";

const data = JSON.parse(fs.readFileSync("list.json", "utf-8"));

// BIN: 6 veya 8 hane (ör: 979286 veya 97928700)
// Veri aralığı: CardRangeStart / CardRangeEnd (PAN aralığı)
function findByBin(binStr) {
  const rangeDigits = String(data[0].CardRangeStart).length;
  const pow = rangeDigits - binStr.length;

  let scale = 1;
  for (let i = 0; i < pow; i++) scale *= 10;

  const start = Number(binStr) * scale;
  const end = start + (scale - 1);

  return data.find(r => r.CardRangeStart <= end && r.CardRangeEnd >= start) || null;
}

const bin = "97928700";
const found = findByBin(bin);

console.log(found);
```

### Python
```py
import json

with open("list.json", "r", encoding="utf-8") as f:
    data = json.load(f)

def find_by_bin(bin_str: str):
    range_digits = len(str(data[0]["CardRangeStart"]))
    pow_ = range_digits - len(bin_str)

    scale = 10 ** pow_
    start = int(bin_str) * scale
    end = start + (scale - 1)

    return next((r for r in data if r["CardRangeStart"] <= end and r["CardRangeEnd"] >= start), None)

bin_value = "979286"
found = find_by_bin(bin_value)

print(found)
```

### PHP
```php
<?php

$data = json_decode(file_get_contents("list.json"), true);

function findByBin(array $data, string $binStr) {
  $rangeDigits = strlen((string)$data[0]["CardRangeStart"]);
  $pow = $rangeDigits - strlen($binStr);

  $scale = 1;
  for ($i = 0; $i < $pow; $i++) $scale *= 10;

  $start = (int)$binStr * $scale;
  $end = $start + ($scale - 1);

  foreach ($data as $r) {
    if ($r["CardRangeStart"] <= $end && $r["CardRangeEnd"] >= $start) return $r;
  }
  return null;
}

$bin = "97928700";
$found = findByBin($data, $bin);

var_dump($found);
```

### Go
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
	"strconv"
)

type Row struct {
	CardRangeStart     int64  `json:"CardRangeStart"`
	CardRangeEnd       int64  `json:"CardRangeEnd"`
	BankName           string `json:"BankName"`
	CardType           string `json:"CardType"`
	IndividualBusiness string `json:"IndividualBusiness"`
	VirtualPhysical    string `json:"VirtualPhysical"`
	CardScheme         string `json:"CardScheme"`
	PANLength          int    `json:"PANLength"`
	BINLength          int    `json:"BINLength"`
	Country            string `json:"Country"`
	CardName           string `json:"CardName"`
}

func pow10(n int) int64 {
	var p int64 = 1
	for i := 0; i < n; i++ {
		p *= 10
	}
	return p
}

func findByBin(data []Row, binStr string) *Row {
	rangeDigits := len(strconv.FormatInt(data[0].CardRangeStart, 10))
	pow := rangeDigits - len(binStr)

	scale := pow10(pow)

	binVal, err := strconv.ParseInt(binStr, 10, 64)
	if err != nil {
		return nil
	}

	start := binVal * scale
	end := start + (scale - 1)

	for i := range data {
		if data[i].CardRangeStart <= end && data[i].CardRangeEnd >= start {
			return &data[i]
		}
	}
	return nil
}

func main() {
	b, err := os.ReadFile("list.json")
	if err != nil {
		panic(err)
	}

	var data []Row
	if err := json.Unmarshal(b, &data); err != nil {
		panic(err)
	}

	bin := "979286"
	found := findByBin(data, bin)

	fmt.Printf("%+v\n", found)
}
```

### C#
```csharp
using System;
using System.IO;
using System.Linq;
using System.Text.Json;
using System.Collections.Generic;

public class Row
{
    public long CardRangeStart { get; set; }
    public long CardRangeEnd { get; set; }
    public string BankName { get; set; } = "";
    public string CardType { get; set; } = "";
    public string IndividualBusiness { get; set; } = "";
    public string VirtualPhysical { get; set; } = "";
    public string CardScheme { get; set; } = "";
    public int PANLength { get; set; }
    public int BINLength { get; set; }
    public string Country { get; set; } = "";
    public string? CardName { get; set; }
}

static long Pow10(int n)
{
    long p = 1;
    for (int i = 0; i < n; i++) p *= 10;
    return p;
}

static Row? FindByBin(List<Row> data, string binStr)
{
    int rangeDigits = data[0].CardRangeStart.ToString().Length;
    int pow = rangeDigits - binStr.Length;

    long scale = Pow10(pow);

    long binVal = long.Parse(binStr);
    long start = binVal * scale;
    long end = start + (scale - 1);

    return data.FirstOrDefault(r => r.CardRangeStart <= end && r.CardRangeEnd >= start);
}

var json = File.ReadAllText("list.json");
var data = JsonSerializer.Deserialize<List<Row>>(json) ?? new List<Row>();

var bin = "97928700";
var found = FindByBin(data, bin);

Console.WriteLine(found is null ? "null" : JsonSerializer.Serialize(found));
```
