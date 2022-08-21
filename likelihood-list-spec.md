# Likelihood List Annotation

## Syntax

*Data* **:{ [ *Attr1* | *Attr2* | … ] }[\*]**

| Item | Description |
| :----: | ----------- |
| Data | Primitive data of the list. |
| `:`  | Delimiter of Data and Attr before "{" without space. |
| `{`  | Opening bracket of Attr. |
| Attr | Attributions of the Data. A number and/or strings separated by "\|". |
| `}`  | Closing bracket of Attr. |
| `*`  | Exception flag after "}" without space. |

### Attributions

The "Attr" value is category name or ordered number.
 - Category : Strings of category name. It is *required.* A Data can have multiple category.
 - Order : Integer of enumeration order (Ex. 1 to n). It is optional, but it must be once in the Data. Duplicated number is allowed in the list.


## Likelihood value

All Data has likelihood value which is calculated from Attr.

For the List has no ordered Attr:
 `L1 = L0 * (1 - Cn / Ct)`

For the Data has ordered Attr:
 `L1 = L0 * (1 - Cn / Ct) * (1 / Ndup)`

For the Data doesn't have ordered Attr, but the other data have in the list:
 `L1 = L0 * (1 - Cn / Ct) * (1 - Nt / It)`

- L1 : Likelihood value of the Data.
- L0 : Base value (= 1).
- Cn : Number of categories on the Data.
- Ct : Total number of categories in the list.
- Ndup : Number of duplicated Orders of the Data in the list.
- Nt : Total number of ordered Data in the list.
- It : Total number of Data on the list.
- \* flag : L1 * 0.1


## Views

### Sample list

These list items have mixed Attributes to explain some cases.

```
ITEM1 :{1 | catA | catC}		// 0.333 = 1*(1-2/3)*1/1
ITEM2 :{catA | 2}		// 0.667 = 1*(1-1/3)*1/1
ITEM3 :{8 | catB}		// 0.667
ITEM4 :{6 | catC}*		// 0.067 = 1*(1-1/3)*1/1 * 0.1
ITEM5 :{5}			// 0.500 = 1*(1-0)*1/2
ITEM6 :{5 | catB}		// 0.333 = 1*(1-1/3)*1/2
ITEM7 :{catC | 4 | catB}		// 0.333
ITEM8 :{}		// 0.223 = 1*(1-0)*(1-7/9)
ITEM9 :{catA}		// 0.148 = 1*(1-1/3)*(1-7/9)
```

#### Notes:
 - ":{}" means the Data (ITEM8) belongs to the root category.
 - ":{catA}" means the Data belongs catA category of the root category.
 - "Cn" is 1 - 2.
 - "Ct" is 3. (catA, catB, catC)
 - "Nt" is 7.
 - "It" is 9.
 - "Ndup" of ITEM5 and ITEM6 is 2.

### Ordered List view (sorted by order)

 1. ITEM1
 2. ITEM2
 3. ITEM7
 4. ITEM5
 5. ITEM6
 6. ITEM3

#### Internaly:
 - [ordered list]
   1. ITEM1, 0.333
   2. ITEM2, 0.667
   3. ITEM7, 0.333
   4. ITEM5, 0.500
   5. ITEM6, 0.333
   6. ITEM4, 0.067 (\*omitted)
   7. ITEM3, 0.667
 - [ITEM8, 0.223]
 - [ITEM9, 0.148]

 '\*' : excepted items can be omitted.
 '[]' : ITEM8 and ITEM9 are excluded from ordered items (hidden).

### Categorized List view (sorted by category name and order)

 - catA: ITEM1, ITEM2, ITEM9
 - catB: ITEM7, ITEM6, ITEM3
 - catC: ITEM1, ITEM7

#### Categorized List (unsorted)

 - {}: (ITEM5, ITEM8)	// 0.278 = 0.333 + 0.223 / 2
 - catA: ITEM1, ITEM2, ITEM9	// 0.383 = 0.333 + 0.667 + 0.148 / 3
 - catC: ITEM1, ITEM7,(ITEM4\*)	// 0.333 = 0.333 + 0.333 / 2
 - catB: ITEM3, ITEM6, ITEM7	// 0.444 = 0.667 + 0.333 + 0.333 / 3

 '{}': uncategorized or root items can be omitted.
 '\*' : excepted items can be omitted.

### Map view (sorted by order and category name)

|   |catA | catB | catC |
| :-: | :-: | :-: | :-: |
| 1 | ITEM1 | - | ITEM1 |
| 2 | ITEM2 | - | - |
| 4 | - | ITEM7 | ITEM7 |
| 5 | - | ITEM6 | - |
| 8 | - | ITEM3 | - |
| _ | ITEM9 | - | - |

#### Map view (unsorted)

|     |catA | catC | catB | {} |
| :-: | :-: | :-: | :-: | :-: |
| 1   | ITEM1 | ITEM1 | - | - |
| 2   | ITEM2 | - | - | - |
| 8   | - | - | ITEM3 | - |
| (6) | - | (ITEM4)\* | - | - |
| 5   | - | - | ITEM6 | (ITEM5) |
| 4   | - | ITEM7 | ITEM7 | - |
| _   | ITEM9 | - | - | (ITEM8) |

 '{}'(uncategorized) and '*'(excepted item) can be omitted.


## n-Dimention List (nested Attr)

*(TBD)*

rev. 20220212
