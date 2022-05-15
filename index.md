---
layout: default
title: Home
---
{% assign totalPortfolioValue = 1.0 %}
{% for account in site.data.accounts %}
{% assign totalPortfolioValue = totalPortfolioValue | plus: account.value %}
{% endfor %}

# Enchilada Portfolio Tracker

## Total Portfolio Value: ${{ totalPortfolioValue | round }}

## Asset Allocation

<!-- get the list of all account holdings -->
{% assign allAccountHoldings = site.data.account-holdings %}

<table class="usa-table width-tablet">
<thead>
<th>Class</th>
<th class="text-right">Total ($)</th>
<th class="text-right">Actual (%)</th>  
<th class="text-right">Target (%)</th>
</thead>

<!-- For each asset class... -->
{% for class in site.data.classes %}
<!-- returns Total Stock Market, Cash, etc. -->

<!-- Set a subtotal variable that is unique to each asset class -->
{% assign subTotal = forloop.index | prepend:"class.name" %}

<tr>
<td>{{ class.name }} ({{ class.abbreviation }})</td>

<td class="text-right">
<!-- Get the list of holdings for that asset class. -->
{% assign classHoldings = site.data.holdings | where: "class", class.abbreviation %}
<!-- returns Berkshire for TSM, Cash for CSH, etc. -->

<!-- For each holding in the class... -->
{% for classHolding in classHoldings %}

<!-- For each holding across all accounts... -->
{% for accountHolding in allAccountHoldings %}

<!-- Filter the holdings across all accounts by the symbols for each class -->
{% assign matchedHoldings = accountHolding.holdings | where: "symbol", classHolding.symbol %}

<!-- For each of the matching/filtered holdings... -->
{% for holding in matchedHoldings %}

<!-- Update the subtotal variable by adding the value of each holding to it. -->
{% assign subTotal = subTotal | plus: holding.value %}

{% endfor %}
{% endfor %}
{% endfor %}

<!-- Show the final results of the subtotal variable for each class. -->
{{ subTotal }}
</td>

<td class="text-right">{{ subTotal | divided_by: totalPortfolioValue | times: 100 | round }}</td>

<td class="text-right">{{ class.target }}</td>
</tr>
{% endfor %}
</table>

## Account Holdings

{% for item in site.data.account-holdings %}
{% assign total = 0 %}
<h3>{{ item.name }}</h3>
<table class="usa-table width-mobile-lg">
<thead>
<th>Holding</th>
<th class="text-right">Value</th>
</thead>
{% for holding in item.holdings %}
{% assign total = total | plus: holding.value %}
<tr>
<td>{{ holding.symbol }}</td>
<td class="text-right">{{ holding.value }}</td>
</tr>
{% endfor %}
<tr>
<td><span class="text-bold">Total</span></td>
<td class="text-right"><span class="text-bold">{{ total }}</span></td>
</tr>
</table>
{% endfor %}

## Holdings

{% for item in site.data.holdings %}
<h3>{{item.name}}</h3>
<ul>
<li>Symbol: {{item.symbol}}</li>
<li>Class: {{item.class}}</li>
<li>Type: {{item.type}}</li>
</ul>
{% endfor %}

## Accounts

{% for item in site.data.accounts %}
<h3>{{item.name}}</h3>
<ul>
<li>{{item.institution}}</li>
<li>{{item.treatment}}</li>
<li>{{item.value}}</li>
</ul>
{% endfor %}

## Classes

{% for item in site.data.classes %}
<h3>{{item.name}}</h3>
<ul>
<li>{{item.abbreviation}}</li>
<li>{{item.stance}}</li>
<li>{{item.target}}</li>
</ul>
{% endfor %}
