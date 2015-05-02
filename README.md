# uno

To produce the test data from its source:

```bash
./data.sh
## currently 669 unique names in names_all.txt
```

To make the best-guess merge table:

```bash
cat names_all.txt | ./uno.py > names_uno.txt
```

It looks like the only problem is with the Plíšková twins. But Karolína played in the [2013 US Open](http://en.wikipedia.org/wiki/2013_US_Open_%E2%80%93_Women%27s_Singles), so we only have to edit data rows two and three. The result is in `names_merge.txt`.

How many unique players are there?

```bash
tail +2 names_merge.txt | cut -d, -f2 | sort | uniq | wc -l
# 359
```

So who played the most in these events?

```bash
join -t, <(sort names_all.txt) <(sort names_merge.txt) | cut -d, -f2 | sort | uniq -c | sort -nr | head
#  24 Novak Djokovic
#  22 Rafael Nadal
#  21 Serena Williams
#  21 David Ferrer
#  20 Na Li
#  19 Victoria Azarenka
#  19 Agnieszka Radwanska
#  18 Stanislas Wawrinka
#  17 Tommy Robredo
#  17 Sloane Stephens
```

---

Thoughts so far:

 * Levenshtein distance is nice, but it turned out I could quickly get a "distance" with near-perfect behavior just on zero/nonzero grouping by writing a custom one.
     * This amounted to writing a function to extract a "key" which in the case of the tennis players was their first initial and a regex-determined "last name". Knowing this would work depended on looking at the data.
     * With a more usual distance metric, it would be nice to see a distribution of pairwise distances, which might suggest whether there are natural breakpoints.
 * It was easy to find one type of error by looking at groups that became too big.
     * It was nice to look at these as their groups rather than in some columnar format - the final output of a merge table is useful but not very readable.
     * To fix the issue I had to go back to the original site of the data and then find extra information from another source. Is there a way to make at least the first part easier?
     * It would probably be good to see a distribution of group sizes - "100 pairs, 20 singletons, 1 group of fifteen" etc.
 * It's hard to know if I missed errors in the smaller groups and singletons who didn't get matched.

 * Can I use tSNE (or similar) to get a visualization based just on a distance matrix?
