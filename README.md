## Using the API endpoints

- Domain: sagaapitest-westeurope-001.azurewebsites.net
- Ask romain.lhotte@silho.tech for an API key.

Example usage (Python3):
```py
PS C:\Users\lhott> python
Python 3.12.5 (tags/v3.12.5:ff3bc82, Aug  6 2024, 20:45:27) [MSC v.1940 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import requests
>>> url: str = "https://sagaapitest-westeurope-001.azurewebsites.net/api/BackgroundNoiseOptions?code=<YOUR_API_KEY>"
>>> requests.get(url).text
'[{"name":"DoNothing","id":0},{"name":"MinLocus","id":1},{"name":"MinLocusAggressive","id":2}]'
>>>                                                                                                                                                                                
```

1/ The first thing to test is the /api/TestPostRequest endpoint. You should make a POST request with an empty payload.
You should get a 200 OK response with the following payload:
```
{
    "message": "Success!"
}
```
This is just to make sure everything is OK.


2/ Once that works, you can use the /api/AttributeLists endpoint.
You should make a GET request. You should get a 200 OK response with the following payload:
```
[
    {
        "label": "Serological Groups and Antigen-Specific Antibodies",
        "comment": "Antibodies are restricted to targetting either Serological groups (e.g., A2) or Antigens (e.g., A*02:01), both of which are fully represented. "Special" Saint-Louis Hospital attributes (e.g., QA3a23 or QA456) are excluded, including AlphaBeta attributes. Note: we use the term antigen-specific antibodies, which you may also see referred to as allele-specific antibodies in the literature (they are used interchangeably).",
        "id": 1
    },
    ...
    {
        "label": "Serological Groups, Antigen-Specific and Multi-Antigen Antibodies - SLS Version",
        "comment": "Antibodies can only target Serological Groups (e.g., A2), Multiple Antigens (e.g., A*02:01/A*02:06) and Individual Antigens (e.g., A*02:06). Not all antigens and serological groups are included. Additional special attributes (such as QA3a23, or QA456) are present. Can include AlphaBeta attributes if user asks for them. See documentation for more details. Note: we use the term antigen-specific antibodies, which you may also see referred to as allele-specific antibodies in the literature (they are used interchangeably).",
        "id": 6
    }
]
```

This will get you a description of the different types of analyses that can be performed.
Once you chose the analysis you want to perform, keep its id (`attributeListId` field of the SerumAnalysis API
endpoint request).


3/ Once that works, you can use the /api/ThresholdLists endpoint.
You should make a GET request. You should get a 200 OK response with the following payload:
```
[
    {
        "id": 1,
        "label": "Bead-by-bead thresholding",
        "comment": "Bead-by-bead thresholding of single antigen assays. LabScreen. https://doi.org/10.1002/eji.202451181.",
        "compatibleKits": [
            "LABScreen Lot 1",
            ...
            "LABScreen Lot 16",
            "LABScreen Lot 17",
        ]
    },
    ...
]
```

This will get you a description of the different threshold lists that can be used.
Once you chose the analysis you want to perform, keep its id (`analyseThresholdId` field of the SerumAnalysis API
endpoint request).
If you do not want a custom threshold list, use 0 (it is also returned by the API).


4/ Once that works, you can use the /api/BackgroundNoiseOptions endpoint.
You should make a GET request. You should get a 200 OK response with the following payload:
```
[
    {
        "name": "DoNothing",
        "id": 0
    },
    {
        "name": "MinLocus",
        "id": 1
    },
    {
        "name": "MinLocusAggressive",
        "id": 2
    }
]
```


5/ Once that works, you can use the /api/AutoAntibodyOptions endpoint.
You should make a GET request. You should get a 200 OK response with the following payload:
```
[
    {
        "name": "DoNothing",
        "id": 0
    },
    {
        "name": "RemoveAll",
        "id": 1
    },
    {
        "name": "RemoveCertainOnly",
        "id": 2
    }
]
```

This will get you a description of the different ways Auto Antibodies can be dealt with.
Once you chose the way to handle Auto Antibodies, keep its id (`autoAntibodyOptionId` field of the SerumAnalysis API
endpoint request).


6/ Once that works, you can use the /api/ImputationModeOptions endpoint.
You should make a GET request. You should get a 200 OK response with the following payload:
```
[
    {
        "name": "None",
        "id": 0
    },
    {
        "name": "HaploDeep",  // !!THIS DOES NOT WORK YET!!
        "id": 1
    },
    {
        "name": "HaploSFHI",
        "id": 2
    }
]
```

This will get you a description of the different ways typings that aren't at the two-field resolution can be dealt with.
Once you chose the way to handle imputation, keep its id (`imputationModeId` field of the SerumAnalysis API
endpoint request).


7/ Finally, you can use the /api/SerumAnalysis endpoint.
You should make a POST request with the following payload format:
```
{
    "requestID": 3,
    "analyseParameters": {
        "attributeListId": 2,
        "alphaBeta": false,
        "analyseThresholdId": 0,
        "thresholdOffset": 0,
        "backgroundNoiseOptionId": 0, 
        "analyseLimitLow": 7,
        "analyseLimitMax": 15,
        "analyseSolver": "recursive_grid_search",
        "autoAntibodyOptionId": 0,
        "certainThreshold": 0.99,
        "imputationModeId": 0
    },
    "beads": {
        "NC": 50,
        "PC": 21000,
        "DQA1*02:01,DQB1*02:01": 2000,
        "DQA1*02:01,DQB1*02:02": 0,
        "DQA1*02:01,DQB1*03:03": 0,
        "DQA1*03:02,DQB1*03:03": 0,
        "DQA1*04:01,DQB1*02:01": 0
    },
    "typings": {
        "recipient": {
            "A_1": "A*02:01",
            "A_2": "A*32:01",
            "B_1": "B*27:05",
            "B_2": "B*44:03",
            "C_1": "C*01:02",
            "C_2": "C*16:01",
            "DRB1_1": "DRB1*01:01",
            "DRB1_2": "DRB1*07:01",
            "DQB1_1": "DQB1*02:02",
            "DQB1_2": "DQB1*05:01",
            "DRB345_1": "DRB4*01:01",
            "DRB345_2": "Nop",
            "DQA1_1": "DQA1*01:01",
            "DQA1_2": "DQA1*02:01",
            "DPB1_1": "DPB1*02:01",
            "DPB1_2": "DPB1*02:01",
            "DPA1_1": "DPA1*01:03",
            "DPA1_2": "DPA1*02:01"
        },
        "donor": {
            "A_1": "A*01:01",
            "A_2": "A*24:02",
            "B_1": "B*39:06",
            "B_2": "B*49:01",
            "C_1": "C*07:01",
            "C_2": "C*07:02",
            "DRB1_1": "DRB1*11:01",
            "DRB1_2": "DRB1*12:01",
            "DQB1_1": "DQB1*03:01",
            "DQB1_2": "DQB1*03:01",
            "DRB345_1": "DRB3*02:02",
            "DRB345_2": "DRB3*02:02",
            "DQA1_1": "DQA1*05:05",
            "DQA1_2": "DQA1*05:05",
            "DPB1_1": "DPB1*04:01",
            "DPB1_2": "DPB1*10:01",
            "DPA1_1": "DPA1*01:03",
            "DPA1_2": "DPA1*02:01"
        }
    }
}
```
Beads' MFI values can be integers or floats.
If you do not have information about a particular typing, you can leave the field empty (empty string). For now,
HaploDeep is not deployed. Alternatively, you can use HaploSFHI.
The "DSAgA" field will be empty if you do not give any information about the donor's typing.
If you have the information that the patient _does not_ have DRB345 alleles, write "Nop".

Imputation:
- HaploDeep is not deployed.
- HaploSFHI will not impute anything if there isn't at least information on loci -A, -B, -DRB1, and -DQB1. Similarly, 
instead, imputation will be performed by picking _a_ typing consistent with the low/intermediate-resolution typing 
provided and equal probabilities to all possible typings will be assigned.
- If ImputationModeId is set to 0 (ImputationMode = None, cf. above), no imputation will be performed. Loci with 
high-resolution typings will be used as-is. Loci with low-resolution typings have undefined behaviour.

The API will consider an empty field for a variant of a gene as an indication for homozygosity if the other variant is
indicated.
You should get a 200 OK response with the following payload:
```
{
    "input": ...,
    "result": {
        "beads": [
            {
                "name": "DQA1*02:01,DQB1*02:01",
                "mfi": 2000,
                "locus": "Dq",
                "attributes": ["DQ2", ...],
                "negativeAttributes": [...],
                "autoAttributes": ["QA2", ...],
                "detectionThreshold": 300
            },
            {
                "name": "DQA1*02:01,DQB1*03:03",
                "mfi": 0,
                "locus": "Dq",
                "attributes": [...],
                "negativeAttributes": ["DQ9", ...],
                "autoAttributes": ["QA2", ...],
                "detectionThreshold": 300
            },
            ...
        ],
        "antibodies": {
            "antibodyName": antibodyValue,
            ...
        },
        "dsaga": {
            {
                "certain": {
                    "antibodyName": antibodyValue,
                    ...
                }
                "probable": {
                    "antibodyName": antibodyValue,
                    ...
                }
                "possibleImputation": {
                    "antibodyName": antibodyValue,
                    ...
                }
                "possibleNotInKit": [
                    "antibodyName",
                    ...
                ]
                "nonExcluded": {
                    "antibodyName": antibodyValue,
                    ...
                } // TODO
            }
        },
        "nonExplainedBeads": [
            nonExplainedBead1Name,
            nonExplainedBead2Name,
            ...
        ],
        "imputedTypings": {
            "recipient": {
                "A_1": {
                    "name": "A*02:01", 
                    "probability": 1.0
                },
                "A_2": {
                    "name": "A*32:01", 
                    "probability": 1.0
                },
                "B_1": {
                    "name": "B*27:05", 
                    "probability": 1.0
                },
                ...,
                "DPA1_2": {
                    "name": "DPA1*02:01",
                    "probability": 1.0
                }
            },
            "donor": {
                ...
            }
        }
    },
    "info": {
        "SimulatedAnnealingUsed": false,
        "TimeSpentAnalysing": 234, // in milliseconds
        "Versions": {
            "HaploDeep": "1.0",
            "HaploSfhi": "2.6",
            "CoreDatabase": "1.0",
            "CoreAlgorithm": "1.0",
            ...
        }
    }
}
```
Note that if you do not get a 200 OK response, you can get the error message by checking the response body.
If you do not get a 200 OK response, you should have some information about why the request failed in the response body.
E.g., you might get: "Donor Typing is not valid." in the response body.

Precisions on the response:
- Bead fields:
  - `Bead.attributes` => attributes that SAgA was allowed to pick from to explain the bead.
  - `Bead.negativeAttributes` => attributes that SAgA was not allowed to pick from to explain the bead because the 
attribute was present on at least one bead the MFI of which was less than the bead's detection threhsold.
  - `Bead.autoAttributes` => attributes that SAgA was not allowed to pick from to explain the bead because the attribute
was present on the recipient's typing. The way this is dealt with depends on the `autoAntibodyOptionId` you chose.
- Some attributes may be present in the form `Attribute_1|Attribute_2|...|Attribute_n` (e.g., A1|A*01:01). This is 
because the list of antibodies, that SAgA was allowed to choose from to explain the serum, contained two or more
antibodies targetting exactly the same beads. So, there is no way for SAgA to differentiate those two attributes.
- `imputedTypings` contained the imputed (if asked to) typings for the recipient and donor with the associated
probabilities. In this example, all the probabilities are at 1.0 because no imputation was needed, everything was 
provided in high-resolution already. If some loci weren't imputed (because imputation didn't happen at all or 
because imputation was partial), those loci will have null values for the name and probability fields.


7/ You can also use the /api/SupportedAlleles endpoint.
You should make a GET request. You should get a 200 OK response with the following payload (a simple array of strings):
```
[
    "A*01:01", "A*01:02", "A*01:03", "A*01:04N", "A*01:06", "A*01:07", ...
]
```
This will get you a list of all the alleles that are currently supported by SAgA - they can be used as a part of a bead
or as a high-resolution typing for a recipient/donor. This list is different from the one HaploDeep supports
(see https://github.com/JasonMendoza2008/HLATypingImputationBenchmarks/releases/tag/HaploDeep).


8/ You can use the /api/AnalyseHistory endpoint to run multiple analyses from the same patient at once.
They will be run in a multi-threaded fashion, but you will get the results in a single response when they're all done.
You should make a POST request with the following payload format:
```
{
    "requestID": 7,
    "analyseParameters": {
        "attributeListId": 2,
        "alphaBeta": false,
        "analyseThresholdId": 0,
        "thresholdOffset": 0,
        "unacceptableThreshold": 2000,
        "backgroundNoiseOptionId": 0, 
        "analyseLimitLow": 7,
        "analyseLimitMax": 15,
        "analyseSolver": "recursive_grid_search",
        "autoAntibodyOptionId": 0,
        "certainThreshold": 0.99,
        "imputationModeId": 0
    },
    "sera": [
        {
            "beads": {
                "NC": 50,
                "PC": 21000,
                "DQA1*02:01,DQB1*02:01": 1000,
                "DQA1*02:01,DQB1*02:02": 1000,
                "DQA1*02:01,DQB1*03:03": 0,
                "DQA1*03:02,DQB1*03:03": 0,
                "DQA1*04:01,DQB1*02:01": 1000
            },
            "date": "2025/02/26"
        },
        {
            "beads": {
                "NC": 50,
                "PC": 21000,
                "DQA1*02:01,DQB1*02:01": 12000,
                "DQA1*02:01,DQB1*02:02": 12000,
                "DQA1*02:01,DQB1*03:03": 0,
                "DQA1*03:02,DQB1*03:03": 0,
                "DQA1*04:01,DQB1*02:01": 12000
            },
            "date": "2025/04/26"
        },
        ...
    ],
    "typings": {
        "recipient": {
            "A_1": "A*02:01",
            "A_2": "A*32:01",
            "B_1": "B*27:05",
            "B_2": "B*44:03",
            "C_1": "C*01:02",
            "C_2": "C*16:01",
            "DRB1_1": "DRB1*01:01",
            "DRB1_2": "DRB1*07:01",
            "DQB1_1": "DQB1*02:02",
            "DQB1_2": "DQB1*05:01",
            "DRB345_1": "DRB4*01:01",
            "DRB345_2": "Nop",
            "DQA1_1": "DQA1*01:01",
            "DQA1_2": "DQA1*02:01",
            "DPB1_1": "DPB1*02:01",
            "DPB1_2": "DPB1*02:01",
            "DPA1_1": "DPA1*01:03",
            "DPA1_2": "DPA1*02:01"
        },
        "donor": {
            "A_1": "A*01:01",
            "A_2": "A*24:02",
            "B_1": "B*39:06",
            "B_2": "B*49:01",
            "C_1": "C*07:01",
            "C_2": "C*07:02",
            "DRB1_1": "DRB1*11:01",
            "DRB1_2": "DRB1*12:01",
            "DQB1_1": "DQB1*03:01",
            "DQB1_2": "DQB1*03:01",
            "DRB345_1": "DRB3*02:02",
            "DRB345_2": "DRB3*02:02",
            "DQA1_1": "DQA1*05:05",
            "DQA1_2": "DQA1*05:05",
            "DPB1_1": "DPB1*04:01",
            "DPB1_2": "DPB1*10:01",
            "DPA1_1": "DPA1*01:03",
            "DPA1_2": "DPA1*02:01"
        }
    }
}
```
Date format is `yyyy/MM/dd`. Everything else is the same as for the `/api/SerumAnalysis` endpoint, except for the
parameter `greyZoneThreshold`.
You should get a 200 OK response with the following payload:
```
{
    "rawResults": [
        {
            "input": ...,
            "date": "2025/02/26",
            "result": {
                "beads": [
                    {
                        "name": "DQA1*02:01,DQB1*02:01",
                        "mfi": 1000,
                        "locus": "Dq",
                        "attributes": ["DQ2", ...],
                        "negativeAttributes": [...],
                        "autoAttributes": ["QA2", ...],
                        "detectionThreshold": 300
                    },
                    ...
                    {
                        "name": "DQA1*02:01,DQB1*03:03",
                        "mfi": 0,
                        "locus": "Dq",
                        "attributes": [...],
                        "negativeAttributes": ["DQ9", ...],
                        "autoAttributes": ["QA2", ...],
                        "detectionThreshold": 300
                    },
                    ...
                ],
                "antibodies": {
                    "antibodyName": antibodyValue,
                    ...
                },
                "dsaga": {
                    {
                        "certain": {
                            "antibodyName": antibodyValue,
                            ...
                        }
                        "probable": {
                            "antibodyName": antibodyValue,
                            ...
                        }
                        "possibleImputation": {
                            "antibodyName": antibodyValue,
                            ...
                        }
                        "possibleNotInKit": [
                            "antibodyName",
                            ...
                        ]
                        "nonExcluded": {
                            "antibodyName": antibodyValue,
                            ...
                        } // TODO
                    }
                },
                "nonExplainedBeads": [
                    nonExplainedBead1Name,
                    nonExplainedBead2Name,
                    ...
                ]
            },
            "info": {
                "SimulatedAnnealingUsed": false,
                "TimeSpentAnalysing": 234, // in milliseconds
                "Versions": {
                    "HaploDeep": "1.0",
                    "CoreDatabase": "1.0",
                    "CoreAlgorithm": "1.0",
                    ...
                }
            }
        },
        {
            "input": ...,
            "date": "2025/04/26",
            "result": {
                "beads": [
                    {
                        "name": "DQA1*02:01,DQB1*02:01",
                        "mfi": 12000,
                        "locus": "Dq",
                        "attributes": ["DQ2", ...],
                        "negativeAttributes": [...],
                        "autoAttributes": ["QA2", ...],
                        "detectionThreshold": 300
                    },
                    ...
                    {
                        "name": "DQA1*02:01,DQB1*03:03",
                        "mfi": 0,
                        "locus": "Dq",
                        "attributes": [...],
                        "negativeAttributes": ["DQ9", ...],
                        "autoAttributes": ["QA2", ...],
                        "detectionThreshold": 300
                    },
                    ...
                ],
                "antibodies": {
                    "antibodyName": antibodyValue,
                    ...
                },
                "dsaga": {
                    {
                        "certain": {
                            "antibodyName": antibodyValue,
                            ...
                        }
                        "probable": {
                            "antibodyName": antibodyValue,
                            ...
                        }
                        "possibleImputation": {
                            "antibodyName": antibodyValue,
                            ...
                        }
                        "possibleNotInKit": [
                            "antibodyName",
                            ...
                        ]
                        "nonExcluded": {
                            "antibodyName": antibodyValue,
                            ...
                        } // TODO
                    }
                },
                "nonExplainedBeads": [
                    nonExplainedBead1Name,
                    nonExplainedBead2Name,
                    ...
                ]
            },
            "info": {
                "SimulatedAnnealingUsed": false,
                "TimeSpentAnalysing": 234, // in milliseconds
                "Versions": {
                    "HaploDeep": "1.0",
                    "CoreDatabase": "1.0",
                    "CoreAlgorithm": "1.0",
                    ...
                }
            }
        },
        ...
    ],
    "imputedTypings": {
        "recipient": {
            "A_1": {
                ...
            },
            ...
        },
        "donor": {
            ...
        }
    },
    "antibodyHistory": {
        "DQ2": {
            dateFirstPositivity: 2025/04/26,
            mfiFirstPositivity: 12000,
            dateLastPositivity: 2025/04/26,
            mfiLastPositivity: 12000,
            dateMaxPositivity: 2025/04/26,
            mfiMaxPositivity: 12000,
            numberSeraPositive: 1,
            numberSeraGreyZone: 1,
            numberTimesAnalysed: 2,
            dateLastSearch: 2025/04/26
        },
        "antibody2Name": {
            ...
        },
        ...
    },
    "dsaHistory": {
        // TODO
    }    
}
```
If the attribute was searched for but never positive, `mfiFirstPositivity`, `mfiLastPositivity` and `mfiMaxPositivity`
will be set to -1 and `dateFirstPositivity`, `dateLastPositivity` and `dateMaxPositivity` will be set to an empty 
string.
If an antibody appears in two different sera with the same MFI and if that MFI happens to be the maximum MFI for that
patient, then `dateMaxPositivity` will be set to the date of the most recent serum with that MFI.
Note that the `imputedTypings` field is at the root of the response, since the imputed typings will be the same
for all the analyses performed for that patient.

