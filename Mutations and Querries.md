## Example 1 :
1:Load Data

   
    
    Mutate:
     {
    set {
    _:michael <name> "Michael" .
    _:michael <age> "39" .
    _:michael <friend> _:amit .
    _:michael <friend> _:sarah .
    _:michael <friend> _:sang .
    _:michael <friend> _:catalina .
    _:michael <friend> _:artyom .
    _:michael <owns_pet> _:rammy .
    _:amit <name> "अमित"@hi .
    _:amit <name> "অমিত"@bn .
    _:amit <name> "Amit"@en .
    _:amit <age> "35" .
    _:amit <friend> _:michael .
    _:amit <friend> _:sang .
    _:amit <friend> _:artyom .

    _:luke <name> "Luke"@en .
    _:luke <name> "Łukasz"@pl .
    _:luke <age> "77" .

    _:artyom <name> "Артём"@ru .
    _:artyom <name> "Artyom"@en .
    _:artyom <age> "35" .

    _:sarah <name> "Sarah" .
    _:sarah <age> "55" .

    _:sang <name> "상현"@ko .
    _:sang <name> "Sang Hyun"@en .
    _:sang <age> "24" .
    _:sang <friend> _:amit .
    _:sang <friend> _:catalina .
    _:sang <friend> _:hyung .
    _:sang <owns_pet> _:goldie .

    _:hyung <name> "형신"@ko .
    _:hyung <name> "Hyung Sin"@en .
    _:hyung <friend> _:sang .

    _:catalina <name> "Catalina" .
    _:catalina <age> "19" .
    _:catalina <friend> _:sang .
    _:catalina <owns_pet> _:perro .

    _:rammy <name> "Rammy the sheep" .

    _:goldie <name> "Goldie" .

    _:perro <name> "Perro" .
     }
    }

>   
     Response:
     {
	  "data": {
	    "code": "Success",
	    "message": "Done",
	    "uids": {
	      "amit": "0x2ed834",
	      "artyom": "0x2ed831",
	      "catalina": "0x2ed835",
	      "goldie": "0x2ed832",
	      "hyung": "0x2ed838",
	      "luke": "0x2ed837",
	      "michael": "0x2ed833",
	      "perro": "0x2ed839",
	      "rammy": "0x2ed836",
	      "sang": "0x2ed83b",
	      "sarah": "0x2ed83a"
	    }
	  },
	  "extensions": {
	    "server_latency": {
	      "parsing_ns": 25400,
	      "processing_ns": 18220796640
	    },
	    "txn": {
	      "start_ts": 80001,
	      "commit_ts": 80003,
	      "preds": [
	        "1-name",
	        "1-_predicate_",
	        "1-friend",
	        "1-age",
	        "1-owns_pet"
	      ]
	    }
	  }
	}


 2.Results using querry
>  
	querry:
	 {
	  michaels_pet(func: eq(name, "Michael")) {
	    name
	    age
	    friend {
	      name@.
	    }
	    owns_pet {
	      name
	    }
	  }
    }
>
	Response:
	{
	  "extensions": {
	    "server_latency": {
	      "parsing_ns": 14965,
	      "processing_ns": 75803428,
	      "encoding_ns": 779644
	    },
	    "txn": {
	      "start_ts": 80005
	    }
	  },
	  "data": {
	    "michaels_pet": [
	      {
	        "name": "Michael",
	        "age": 39,
	        "friend": [
	          {
	            "name@.": "Artyom"
	          },
	          {
	            "name@.": "Amit"
	          },
	          {
	            "name@.": "Catalina"
	          },
	          {
	            "name@.": "Sarah"
	          },
	          {
	            "name@.": "Sang Hyun"
	          }
	        ],
	        "owns_pet": [
	          {
	            "name": "Rammy the sheep"
	          }
	        ]
	      }
	    ]
	  }
	}

3.Language Support
eg: Amit's name in Russian and Korean
>
	Querry:
	{
	  language_support(func: allofterms(name@hi, "अमित")) {
	    name@bn:hi:en
	    age
	    friend {
	      name@ko:ru
	      age
	    }
	  }
	}


>
	  Response:
	  {
	  "extensions": {
	    "server_latency": {
	      "parsing_ns": 45786,
	      "processing_ns": 51674858,
	      "encoding_ns": 1082153
	    },
	    "txn": {
	      "start_ts": 80006
	    }
	  },
	  "data": {
	    "language_support": [
	      {
	        "name@bn:hi:en": "অমিত",
	        "age": 35,
	        "friend": [
	          {
	            "name@ko:ru": "Артём",
	            "age": 35
	          },
	          {
	            "age": 39
	          },
	          {
	            "name@ko:ru": "상현",
	            "age": 24
	          }
	        ]
	      }
	    ]
	  }
	}
4.Filtering,And ,Or & Not

Functions for Filtering:
-   `allOfTerms(edge_name, "term1 ... termN")`: matches nodes with an outgoing  `string`  edge  `edge_name`  where the string contains all listed terms.
    
-   `anyOfTerms(edge_name, "term1 ... termN")`: As with  `allOfTerms`, but matches at least one term.
- The equalities and inequalities can be applied to edges of types:  `int`,  `float`,  `string`  and  `date`

	-   `eq(edge_name, value)`: equal to
	-   `ge(edge_name, value)`: greater than or equal to
	-   `le(edge_name, value)`: less than or equal to
	-   `gt(edge_name, value)`: greater than
	-   `lt(edge_name, value)`: less than


>
	{
	  michael_friends_and(func: allofterms(name, "Michael")) {
	    name
	    age
	    friend @filter(ge(age, 27) AND le(age, 48)) {
	      name@.
	      age
	    }
	  }
	}

>
	Response:
	 {
	  "extensions": {
	    "server_latency": {
	      "parsing_ns": 31864,
	      "processing_ns": 87902376,
	      "encoding_ns": 850393
	    },
	    "txn": {
	      "start_ts": 80027
	    }
	  },
	  "data": {
	    "michael_friends_and": [
	      {
	        "name": "Michael",
	        "age": 39,
	        "friend": [
	          {
	            "name@.": "Artyom",
	            "age": 35
	          },
	          {
	            "name@.": "Amit",
	            "age": 35
	          }
	        ]
	      }
	    ]
	  }
	}
5.Sorting and Pagination

 Results can be ordered using `orderasc` and `orderdesc`.
-   `first: N`  Return only the first  `N`  results
-   `offset: N`  Skip the first  `N`  results
-   `after: uid`  Return the results after  `uid`

>
	Querry:
	{
	  michael_friends_first(func: allofterms(name, "Michael")) {
	    name
	    age
	    friend (orderasc: name@., offset: 1, first: 2) {
	      name@.
	    }
	  }
	}

>
	Response:
	{
	  "extensions": {
	    "server_latency": {
	      "parsing_ns": 32395,
	      "processing_ns": 95518701,
	      "encoding_ns": 808026
	    },
	    "txn": {
	      "start_ts": 80030
	    }
	  },
	  "data": {
	    "michael_friends_first": [
	      {
	        "name": "Michael",
	        "age": 39,
	        "friend": [
	          {
	            "name@.": "Artyom"
	          },
	          {
	            "name@.": "Catalina"
	          }
	        ]
	      }
	    ]
	  }
	}
6.Has, Count and Alias
-  Has: The function `has(edge_name)` returns nodes that have an outgoing edge of the given name.
-  Count : The number of outgoing edges can be counted, using the `count` function.
-  Alias: The output graph can set names for edges in the output with aliasing(alias_name:name).

>
	Querry:
	{
	  have_friends(func: has(friend),first: 4) {
	    name@.
	    age
	    number_of_friends : count(friend)
	  }
	}
>
		Response
		{
		  "extensions": {
		    "server_latency": {
		      "parsing_ns": 37341,
		      "processing_ns": 50700859,
		      "encoding_ns": 1146431
		    },
		    "txn": {
		      "start_ts": 80031
		    }
		  },
		  "data": {
		    "have_friends": [
		      {
		        "name@.": "Alice",
		        "age": 26,
		        "number_of_friends": 2
		      },
		      {
		        "name@.": "Anjali",
		        "age": 26,
		        "number_of_friends": 1
		      },
		      {
		        "name@.": "Michael",
		        "age": 39,
		        "number_of_friends": 5
		      },
		      {
		        "name@.": "Amit",
		        "age": 35,
		        "number_of_friends": 3
		      }
			  ]
			 }
		}
7.Cascade
 
- The `@cascade` directive removes any nodes that don’t have all matching edges in the querry.
>
	 Querry:
	 {
	  michael_friends_with_pets(func: allofterms(name, "Michael")) @cascade {
	    name
	    age
	    friend {
	      name@.
	      owns_pet
	    }
	  }
	}
>
	 Response:
	 "extensions": {
	    "server_latency": {
	      "parsing_ns": 38255,
	      "processing_ns": 130393847,
	      "encoding_ns": 956665
	    },
	    "txn": {
	      "start_ts": 80045
	    }
	  },
	  "data": {
	    "michael_friends_with_pets": [
	      {
	        "name": "Michael",
	        "age": 39,
	        "friend": [
	          {
	            "name@.": "Catalina"
	          },
	          {
	            "name@.": "Sang Hyun"
	          }
	        ]
	      }
	    ]
	  }
	}
8.Normalize
-   returns only edges listed with an alias, and
    
-   flattens the result to remove nesting

>
	Querry:
	{
	  michael_number_friends(func: allofterms(name, "Michael")) @normalize {
	    name : name
	    age
	    number_of_friends : count(friend)
	  }
	}
>
	Response:
		{
		  "extensions": {
		    "server_latency": {
		      "parsing_ns": 12743,
		      "processing_ns": 75081860,
		      "encoding_ns": 965697
		    },
		    "txn": {
		      "start_ts": 80046
		    }
		  },
		  "data": {
		    "michael_number_friends": [
		      {
		        "name": "Michael",
		        "number_of_friends": 5
		      }
		    ]
		  }
		}








