## Museum v9.2

##### Components
- catalog : content-item
- constructeur: Alert for near-misses. Attribute to catalog. Same title can be used in different countries.
- marque: (keyword) a marque can be used by more than 1 constructeur. Alert for near-misses.
- product: a keyword related to a catalog, or constructeur. Alert for near-misses.
- article : a content-item.
- auteur: has identity - many-to-many relation with articles, Alert for near-misses.
- publisher: attribute to article. Alert for near-misses.

##### Operations

- operates with new openacs-cms api.
- 2 types of entries: catalog/construteur (1-2) and article/publisher (3-4).
- an article belongs to a publisher and can have many authors.
- a catalog belongs to a constructeur and can have many products (`h2`).
- both catalogs and articles can have many alternate title (alias - translations - aka indexNames).
- official title (main title) is in `h1`.
- for each catalog, create a constructeur if not exist.
- for each catalog, create a section `c.x` if not exist.
- for each catalog, extract marques referencees in that catalog.
- for each article, create a publisher if not exist.
- for each article, create an author if not exist, and create a link (article-author).
- for each article, create a section `a.x` if not exist.

#####  Using Checksum
- to reduce traffic load, each catalog gets a checksum.
- when doing an upload/update, first get a local copy snap shot from the database.
- the snap-shot contains the catalog's name (unique) and the checksum.
- for each catalog to upload, no need to upload if the checksum is unchanged
- `option.force` (ignore-checksum) can override that function.

##### Sequential-YAML
- yaml may be edited by hand, so it must run with minimal infos and use inherited defaults.
- a constructeur
  - MUST have {title, co}
  - xnor3(title+co) => name (unique)
  - will generate a _constructeur_ item.
    have constructeur name in their data.
- a catalog
  - MUST have {path, title, pic}
  - defaults from context: constructeur
  - should have: {yp, co, ci, sa, products, marques, links}
  - should have: indexNames - or will be visible only as title
  - should have default lang for links, each link can override.
- a marque
  - as stand-alone item, MUST have {label~title, constructeur}
  - if no constructeur, default to context.

##### Les Marques

Here "une marque" is just a label not a trade-mark,
and may be used by several constructeurs.
For each constructeur, we need to record the marques.

wrong:
This means that a label (marque) must be prefixed/postfixed with the constructeur xid.
ix_ref records the first occurence of the label in a catalog.
check if that catalog constructeur same as this-one.

A marque is another kind of keywords.
- we can search all documents for a marque, regardless of the constructeur.
- If a marque is used by more than 1 constructeur, we will decline it in the UI.

##### `cr_keywords`
- used for marques, auteurs, products.

##### `103-import-xlsx-museum`
- check near-miss for marques, auteurs, products, construteurs
- does not write {marques, auteurs, products} into yaml.

##### `116-import-yaml-museum`
- yaml can be edited by hand, so it must run with minimal infos and use inherited defaults,
  and xid is not used here.
- items {marques, auteurs, products} are not mandatory, they will be found again in catalogs and articles.
- TODO: auteur name should be xnor1 or xnor2 to avoid _close miss_.
- yaml should not have item.name.
- therefore use `"catalog": <title>` instead of `"catalog":<name>`
- TODO: missing info about pdf language.

##### ltree

- helping to narrow a search domain.
- if search needs to be done by section => {sec1, sec2, sec3, sec4}
- (\*) if search neeed to be done on catalogs or articles => {c.1, c.2, a.3, a.4}


##### Searches
- find all documents related to a 'marque'.
  That does not specify the constructeur,
  and the same label (marque) can be used by several constructeurs.
  So, entries in index marques....

##### Les Indexes

- list of documents-pdf aggregated by construteur. (sec1-2) => search for con
- list of documents-pdf aggregated by marques @constructeur. (sec1-2)
- list of documents-pdf aggregated by auteur. (sec3-4)
- list of catalogs sorted by title (includes alias/translations)
- list of catalogs sorted by constructeur
- list of articles sorted by publisher
- list of catalogs sorted by products

##### How to build/update l'index des marques.
- why do we need an index-des-marques : too many...
- for Google ? No, need to have it sorted.
- indexes are rebuild when document (catalog) updated/inserted.
- so far we don't need a document (content-item) _author_.

```
{
  "xid": 6506,
  "sec": 2,
  "yp": 1897,
  "h1": "Paul Leboeuf et Guion",
  "h2": [
    "radiateurs de chauffage central"
  ],
  "fr": "Anciennes maisons Gervais (fondée en 1835 rue des Fossés Saint Jacques) et Paul Leboeuf.",
  "mk": [
    "Leguda",
    "Damien",
    "Lutetia"
  ],
  ...
}
```


# ANNEXES

### Catalogues (sec1, sec2)
- indexNames : a catalog can be referenced under different names in the Indexes.
- first in indexNames is the _official_ name and should be `h1` (constructeur name)
- `h2` : products in the catalog.
- each catalog has short description in several languages.
- address is specific to a catalog


```
{
  "xid": 6401,
  "sec": 2,
  "yp": 1867,
  "pic": "1867 Lang",
  "h1": "Lallemant fils et Vappereau",
  "h2": [
    "réchauds"
  ],
  "root": [
    "Lang"
  ],
  "fr": "Réchaud à esprit de vin à flamme forcée.",
  "en": "Forced flame wine spirit stove.",
  "zh": "强压的火焰葡萄蒸馏酒火炉",
  "ci": "Paris",
  "sa": "8 rue Saintonge et 119 rue Vieille du Temple",
  "links": [
    {
      "fn": "1867 Lang 20150702",
      "np": "1"
    }
  ],
  "rev": "Fri Jan 06 2017 23:59:56 GMT+0700 (Indochina Time)",
  "ori": "Collection privée J.Jumeau",
  "deleted": false,
  "restricted": false,
  "transcription": false,
  "co": "France",
  "indexNames": [
    "Lallemand fils et Vappereau",
    "Vappereau"
  ]
}

```


### Articles (sec3, sec4)
- indexNames : a catalog can be referenced under different names in the Indexes.
- first in indexNames is the _official_ name and should be `h1` (constructeur name)
- `h2` : products in the catalog.
- each catalog has short description in several languages.
- address is specific to a catalog

```
{
  "xid": 6419,
  "sec": 3,
  "yp": 1890,
  "pic": "1890 Revue Scientifique chauffage",
  "h1": "Revue Scientifique, N°22, tome XLVI",
  "isoc": "Lancereaux, Chantemesse (A.). Revue Scientifique, N°22, tome XLV, le chauffage des appartements",
  "h2": [
    "le chauffage des appartements"
  ],
  "root": [
    "Lancereaux",
    "A. Chantemesse"
  ],
  "fr": "Revue Scientifique, N°22, tome XLVI. Article: Hygiène, Le chauffage des appartements, les poêles mobiles, par A. Chantemesse",
  "en": "Magazine La revue Scientifique , No. 22, Volume XLVI. Article: Hygiene, The heating of apartments, mobile stoves, by A. Chantemesse",
  "zh": "La revue Scientifique杂志，第22号，Volume XLVI。文章：卫生,公寓的供暖，移动炉灶的加热,  由A. Chantemesse 提供.",
  "links": [
    {
      "fn": "1890 Revue Scientifique chauffage 20161116",
      "np": "6"
    }
  ],
  "rev": "Fri Dec 22 2017 23:59:56 GMT+0700 (Indochina Time)",
  "ori": "collection privée J.Jumeau",
  "deleted": false,
  "restricted": false,
  "transcription": false,
  "co": "France",
  "auteurs": [
    "Lancereaux",
    "Chantemesse (A.)"
  ],
  "indexNames": [
    "Revue Scientifique, N°22, tome XLV, le chauffage des appartements"
  ]
}

```

##### soft-links versus hard-links
https://www.ostechnix.com/explaining-soft-link-and-hard-link-in-linux-with-examples/

####### a soft link
- can cross the file system,
- allows you to link between directories,
- has different inodes number and file permissions than original file,
- permissions will not be updated,
- has only the path of the original file, not the contents.

####### a hard Link : a mirror copy

- can’t cross the file system boundaries,
- can’t link directories,
- has the same inodes number and permissions of original file,
- permissions will be updated if we change the permissions of source file,
- has the actual contents of original file, so that you still can view the contents, even if the original file moved or removed.
