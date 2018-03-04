---
layout: post
title:  "Vectors of Struct References in C++"
date:   2018-03-04
---

I was in the middle of a big C++ project when I found myself creating a vector of struct pointers. I was fairly certain that the structs would stay in memory so that if I modified the struct values, they would update both in the local scope and within the vector.

I wasn't 100% sure that this was the case, though, and the program was complicated enough that if I gambled wrong, debugging it would be complicated. I wrote a small C++ program that acts as a reduce test case to prove that my assumption was correct.

{% highlight c++ %}
#include <vector>
#include <iostream>

struct CoordPair {
    int x, y;

    CoordPair() {
        x = -1;
        y = -1;
    }

    CoordPair(int newX, int newY) {
        x = newX;
        y = newY;
    }
};

int main(int argc, char **argv) {
    std::vector<CoordPair *> pairs;
    CoordPair pair = CoordPair(5, 6);
    pairs.push_back(&pair);

    pair.x = 8;
    pair.y = 12;

    std::cout << "Pair coordinates: (" << pairs[0]->x << ", " << pairs[0]->y
        << ")" << std::endl;

    return 0;
}
{% endhighlight %}

Finally, I compiled and ran it.

```
$ g++ test.cpp
$ ./a.out
Pair coordinates: (8, 12)
```

It works!

