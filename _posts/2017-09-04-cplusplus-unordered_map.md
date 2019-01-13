---
layout: post
title:  "[C/C++] unordered_map"
date:   2017-09-04
comments: true
categories: C/C++
tags: unordered_map
description:
published: true
---


map: 红黑树

unordered_map: hash表


## 1

```c++
#include <iostream>
#include <string>
#include <unordered_map>

using namespace std;

typedef unordered_map<string, string> stringmap;

stringmap merge(stringmap a, stringmap b)
{
	stringmap temp(a);
	temp.insert(b.begin(), b.end());
	return temp;
}

int main()
{
	stringmap first;
	stringmap second({ {"apple", "red"}, {"lemon", "yellow"} });
	stringmap third({ { "orange", "orange" },{ "strawberry", "red" } });
	stringmap fourth(second);
	stringmap fifth(merge(third, fourth));
	stringmap sixth(fifth.begin(), fifth.end());

	for (auto & x : sixth)
	{
		std::cout << " " << x.first << ":" << x.second;
	}
	std::cout << endl;

	return 0;
}
```


## at()

```c++
/*
mapped_type& at ( const key_type& k );
const mapped_type& at ( const key_type& k ) const;
Average case: constant.
Worst case: linear in container size.
*/
#include <iostream>
#include <string>
#include <unordered_map>

using namespace std;

int main()
{
	std::unordered_map<string, int> mymap = {
		{"Mars", 3000},
		{"Saturn", 60000},
		{"Jupiter", 70000}
	};

	mymap.at("Mars") = 3396;
	mymap.at("Saturn") += 272;
	mymap.at("Jupiter") = mymap.at("Saturn") + 9638;

	for (auto & x : mymap)
	{
		std::cout << x.first << ": " << x.second << std::endl;
	}

	return 0;
}
```


## size()


```c++
#include <iostream>
#include <string>
#include <unordered_map>

using namespace std;

int main()
{
	std::unordered_map<string, double> mymap = {
		{"milk", 2.30},
		{"potatoes", 1.90},
		{"eggs", 0.40}
	};

	std::cout << "mymap.size() is " << mymap.size() << std::endl;

	return 0;
}
```


## begin()

```c++
#include <iostream>
#include <string>
#include <unordered_map>

using namespace std;

int main()
{
	unordered_map<string, string> mymap;
	mymap = {
		{"Australia", "Canberra"},
		{"U.S.", "Washington"},
		{"France", "Paris"}
	};

	std::cout << "mymap contains:";
	for (auto it = mymap.begin(); it != mymap.end(); ++it)
		cout << " " << it->first << ":" << it->second;
	cout << endl;

	cout << "mymap's buckets contains:\n";
	for (unsigned i = 0; i < mymap.bucket_count(); ++i)
	{
		cout << "bucket #" << i << " contains:";
		for (auto local_it = mymap.begin(i); local_it != mymap.end(i); ++local_it)
		{
			cout << " " << local_it->first << ":" << local_it->second;
		}
		cout << endl;
	}

	return 0;
}
```


## bucket()

```c++
#include <iostream>
#include <string>
#include <unordered_map>

using namespace std;

int main()
{
	unordered_map<string, string> mymap = {
		{"us", "United States"},
		{"uk", "United Kingdom"},
		{"fr", "France"},
		{"de", "Germany"}
	};

	for (auto & x : mymap)
	{
		cout << "Element [" << x.first << ":" << x.second << "]";
		cout << " is in bucket #" << mymap.bucket(x.first) << endl;
	}

	return 0;
}
```


## bucket_count()


```c++
#include <iostream>
#include <string>
#include <unordered_map>

using namespace std;

int main()
{
	unordered_map<string, string> mymap = {
		{"house", "maison"},
		{"apple", "pomme"},
		{"tree", "arbre"},
		{"book", "livre"},
		{"door", "porte"},
		{"grapefruit", "pamplemousse"}
	};

	unsigned n = mymap.bucket_count();
	
	cout << "mymap has " << n << " buckets.\n";

	for (unsigned i = 0; i < n; i++)
	{
		cout << "bucket #" << i << " contains: ";
		for (auto it = mymap.begin(i); it != mymap.end(i); ++it)
		{
			cout << "[" << it->first << ":" << it->second << "] ";
		}
		cout << endl;
	}

	return 0;
}
```


## bucket_size()

```c++
#include <iostream>
#include <string>
#include <unordered_map>

using namespace std;

int main()
{
	unordered_map<string, string> mymap = {
		{"us", "United States"},
		{"uk", "United Kingdom"},
		{"fr", "France"},
		{"de", "Germany"}
	};

	unsigned nbuckets = mymap.bucket_count();

	cout << "mymap has " << nbuckets << " buckets:\n";

	for (unsigned i = 0; i < nbuckets; i++)
	{
		cout << "bucket #" << i << " has " << mymap.bucket_size(i) << " elements.\n";
	}

	return 0;
}
```


## insert()

```c++
#include <iostream>
#include <string>
#include <unordered_map>

using namespace std;

int main()
{
	unordered_map<string, double> myrecipe;
	unordered_map<string, double> mypantry = {
		{"milk", 2.0},
		{"flour", 1.5}
	};

	pair<string, double> myshopping("baking powder", 0.3);

	myrecipe.insert(myshopping);
	myrecipe.insert(std::make_pair<string, double>("eggs", 0.3));
	myrecipe.insert(mypantry.begin(), mypantry.end());
	myrecipe.insert({ {"sugar", 0.8}, {"salt", 0.1} });

	cout << "myrecipe contains:" << endl;
	for (auto & x : myrecipe)
	{
		cout << x.first << ": " << x.second << endl;
	}
	cout << endl;

	return 0;
}
```


## find()

```c++
#include <iostream>
#include <string>
#include <unordered_map>

using namespace std;

int main()
{
	unordered_map<string, double> mymap = {
		{"mom", 5.4},
		{"dad", 6.1},
		{"bro", 5.9}
	};

	string input;
	cout << "who? ";
	getline(std::cin, input);

	unordered_map<string, double>::const_iterator got = mymap.find(input);
	if (got == mymap.end())
		cout << "not found";
	else
		cout << got->first << " is " << got->second;

	cout << endl;

	return 0;
}
```
