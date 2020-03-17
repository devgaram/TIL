---
layout: post-content
title: Liquid 문법
date: 2019-03-05
tags: [jekyll, liquid]
category: [jekyll]
---

jekyll 블로그 운영시 알아야할 필수 liquid 문법

---

[liquid 문법 정리된 사이트](https://shopify.github.io/liquid/basics/types/)

## Liquid

<table>
<thead>
	<tr><th>category</th><th>Input</th><th>Output</th></tr>
</thead>
<tbody>
	<tr>
		<td>Objects</td>
		<td>
		{ { page.title } }	
		</td>
		<td>Introduction</td>
	</tr>
	<tr>
		<td>Tags</td>
		<td>
		{ % if user % } <br/>
		  &nbsp;&nbsp;Hello { { user.name } }! <br/>
		{ % endif % }
		</td>
		<td>Hello Adam!</td>
	</tr>
	<tr>
		<td>Filters</td>
		<td>
		{ { "/my/fancy/url" | append: ".html" } }
		</td>
		<td>/my/fancy/url.html</td>
	</tr>
	<tr>
		<td>Filters</td>
		<td>
		{ { "adam!" | capitalize | prepend: "Hello " } }
		</td>
		<td>Hello Adam!</td>
	</tr>
	<tr>
		<td>assing String</td>
		<td>
		{ % assign my_string = "Hello World!" % }
		</td>
		<td>String 타입</td>
	</tr>
	<tr>
		<td>assing Number</td>
		<td>
		{ % assign my_int = 25 % }
		</td>
		<td>Number 타입</td>
	</tr>
	<tr>
		<td>assing Boolean</td>
		<td>
		{ % assign foo = true % }
		</td>
		<td>Boolean 타입</td>
	</tr>
	<tr>
		<td>Nil (empty)</td>
		<td>
		The current user is { { user.name } }
		</td>
		<td>The current user is</td>
	</tr>
	<tr>
		<td>Array</td>
		<td>
		{ % for user in site.users % }<br/>
		  &nbsp;&nbsp; { { user } }<br/>
		{ % endfor % }
		</td>
		<td>Tobi Laura Tetsuro Adam</td>
	</tr>
	<tr>
		<td>Array</td>
		<td>
		{ { site.users[0] } }<br/>
		{ { site.users[1] } }<br/>
		{ { site.users[3] } }
		</td>
		<td>Tobi <br/>
		Laura <br/>
		Adam</td>
	</tr>
	<tr>
		<td>plus</td>
		<td>
		{ { 4 | plus: 2 } }
		</td>
		<td>6</td>
	</tr>
	<tr>
		<td>limit</td>
		<td>
		{ % for item in array limit:2 % }<br/>
	       &nbsp;&nbsp; { { item } }<br/>
		{ % endfor % }
		</td>
		<td>limit를 통해 2개까지만 반복문을 돌린다.</td>
	</tr>
</tbody>
</table>
