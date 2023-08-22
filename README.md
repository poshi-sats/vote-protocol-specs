# vote-protocol-specs

# Why
The self-governance of the Ordinals community poses a challenge due to the inherent difficulty in determining which opinion should be adopted as the community consensus. 
To address this, I propose implementing a 𝕍ote protocol within the Ordinals community, which would enable decentralized governance and facilitate decision-making processes.
- Initiate- Deploy a proposal.
- Express- Inscribe a ballot.
- Vote- Transfer the ballot to yourself to complete the voting process.
![image](https://github.com/poshi-sats/vote-protocol-specs/assets/140290499/772eb6ab-0824-445e-ad36-c6c053e8bd27)

Key features of the 𝕍ote protocol: 
- The indexers of the protocol are decentralized, because whoever understands SQL is able to calculate voting results for the proposals they are interested in. By the way, I will build a demo on GeniiData as a reference.
- The governance of the protocol itself is decentralized. You can either leave comments on the 𝕍ote protocol or vote to contribute to its evolution.
- Since the voting process is on chain, people have to pay network fees to initiate or participate in votes. This means only meaningful proposals will be chosen to vote for, thereby incentivizing an enhancement in the quality of proposals within the 𝕍ote protocol. (Low quality proposals will end at a low participation rate. )

# How to
## Creating a proposal
- Anyone can propose ideas on any topic. The community decides on good proposals by participating. 
- To facilitate the smooth upgrade of the 𝕍ote protocol, proposals need to be published in the JSON5 format (note: JSON5 is an extension of JSON).
- While I prefer not to impose excessive constraints on proposal content, it is important to have a clear theme and options for each proposal. Otherwise, the purpose of the voting process becomes uncertain.
- Considering the robustness of the protocol, the expiration time of a proposal is defined by the number of blocks before it's expired, despite the fact that block height might not be intuitive, which can be fixed easily through good interpretation by the indexers. 
- The options should be written in the format of JSON objects. Although it may not be the most comfortable format to work with, it proves to be user-friendly when implementing indexing.
- Proposals can be submitted without options for suggestions and feedback from the community. Ballots that don't match any options won't count towards the proposal but will be considered as comments. Voting tools should allow the community to view these comments as they hold value.
- Please note that once a proposal is published, its unique identifier will be the inscription ID. It is important to clarify that the identifier is not the inscription number, as I have concerns about the reliability and robustness of using inscription numbers for this purpose.
- I understand that there may be additional features to consider when it comes to voting, such as requiring specific assets to participate or weighting each vote based on the amount of assets held. However, I prefer a simpler format for this protocol. Feel free to include any desired features in the description for reference by those who will index it.

## Getting a ballot
Although the proposal inscriptions may be complex, it is important to keep the ballots as straightforward as possible. Voters only need to clearly indicate the inscription ID of the proposal and their preferred choice.

`Proposal Inscription ID: Your Option`

It is crucial to note that possessing a ballot inscription does not equate to casting a vote. (Otherwise, the vote protocol would be susceptible to easy manipulation.)
To maintain consistent indexing behavior within the vote protocol, please strictly adhere to the standardized format definition for the ballot.

## Vote
The only criterion to initiate vote counting is the act of voting itself, which involves sending the ballot to the voter's own address. 
- The source of the ballot, whether received or self-inscribed, is not taken into consideration during the tallying process. Either is OK, as long as the ballot inscription is valid. 
- In a single proposal, only the first submitted ballot from each address is counted as valid. Any subsequent duplicate votes or attempts to alter a vote will be deemed invalid.
- A special reminder for indexers: For a more precise determination of the voting order, consider using the block number and transaction index within the block. However, it is important to note that there is still a possibility of multiple votes for the same proposal within a single transaction. In such instances, the earliest created ballot should be chosen as the valid vote for tallying.

# Operations
## Deploy proposal
To create a vote using the 𝕍ote protocol, inscribe a JSON format inscription to publicly announce the essential information about the proposal, including title, options, etc. 
Then, share the inscription ID with the community to invite them to vote. 
Here's an example:
```json
{
    "p": "vote",
    "title": "vote for 𝕍ote",
    "description": "https://github.com/poshi-sats/vote-protocol-specs",
    "expiration": "50000", 
    "options": 
    {
        "For": "I support the 𝕍ote protocol",
        "Against": "I'm against the 𝕍ote protocol"  
    }  
}
```

| key         | required | expected                          | description                                                        |
|-------------|----------|-----------------------------------|--------------------------------------------------------------------|
| p           | Yes      | vote                              | Helps indexers identify the vote protocol                          |
| title       | Yes      | Title of the proposal             | A concise title of the proposal to be voted on. <br>Use a brief, descriptive title for the proposal up for vote. Put any lengthy, detailed information in the description rather than the title.  |
| description | No       | Information about this vote       | All information concerning this proposal, such as background, the controversy and the goals of the votes. |
| expiration  | No       | Expiration time(number of blocks) | The numerical value entered here represents the number of blocks that will elapse after the creation of this vote before it reaches its deadline and stops accepting any further votes.                                                   |
| options     | No       | List of the options               | List the available options for the vote, together with the description for each option.  |

## Mint ballot
- The inscription ID of the proposal: Chosen options
- The chosen option should be among the options listed in the proposal inscriptions, otherwise it will not be counted on the vote results. 

Here's an example:
- `220185c6d19468d04bb042728843c6c34511ea9f0b854155f74024068cb6b2e4i0: for`

## Vote
**MUST:** Transfer the inscription of the ballot to your own address.
- It doesn't matter which address inscribed the ballot, but the validity of the ballot inscription should be checked.
- Only after you complete the vote process(i.e., send the ballot to yourself), can it be considered a valid vote. 

# Examples

## Regular Example

### Step1. Deploy proposal:
[Deployer] Initiate a proposal by inscribing it using json code, and get the corresponding inscription ID.
eg: 220185c6d19468d04bb042728843c6c34511ea9f0b854155f74024068cb6b2e4i0

```json
{
    "p": "vote",
    "title": "vote for 𝕍ote",
    "description": "https://github.com/poshi-sats/vote-protocol-specs",
    "expiration": "50000", 
    "options": 
    {
        "For": "I support the 𝕍ote protocol",
        "Against": "I'm against the 𝕍ote protocol"  
    }  
}
```
### Step2. Mint ballot
[Voter] Get a ballot by inscribing it using a single line. 
``` 
220185c6d19468d04bb042728843c6c34511ea9f0b854155f74024068cb6b2e4i0: For
```

### Step3. Vote
[Voter] Transfer the ballot to yourself to complete the vote. 
Subsequently, the "for" option count for proposal "vote for 𝕍ote" (220185c6d19468d04bb042728843c6c34511ea9f0b854155f74024068cb6b2e4i0) will increase by 1.

## Invalid examples

``` 
220185c6d19468d04bb042728843c6c34511ea9f0b854155f74024068cb6b2e4i0: Against,For ❌ 
```

The proposal does not include any corresponding options for "Against, For". As a result, they will be treated as comments specifically related to the proposal.

---

```
220185c6d19468d04bb042728843c6c34511ea9f0b854155f74024068cb6b2e4i0: Yes I agree ❌
```
There are whitespaces in the options. 

---

```
1234567890: For ❌
```
 The inscription ID provided does not match any existing inscriptions, or the corresponding proposal is invalid.

 ---

 ```
220185c6d19468d04bb042728843c6c34511ea9f0b854155f74024068cb6b2e4i0 : For ❌
 ```
 Whitespaces between the inscription ID and colon. 

 ---
## Special Vote cases
```json
{
    "p": "𝕍ote",
    "title": "vote for 𝕍ote" 
}
```
- `𝕍ote` is also considered a valid identifier for proposals.
- Since this proposal is deployed without options, all the ballots related to it should be regarded as comments on the proposal.
- In the absence of expiration time in the proposal inscription, the default duration for the vote to remain valid is approximately one month or 5000 blocks.

# Indexing ruleset
## Indexing proposals
| content            | **ruleset**                                                     |
|-------------|------------------------------------------------------------------------|
| Type        | `'text/plain'` or `'application/json'`                                 |
### **key: `"p"` (required)**

| value       | **ruleset**                                                            |
|-------------|------------------------------------------------------------------------|
| Type        | `string`                                                               |
| Regex       | `(?i)^\s*([v𝕍]ote)\s*$`                                                |
| Explanation | Case-insensitive, and leading or trailing whitespaces will be ignored. |
---

### **key: `"title"` (required)**

| value       | **ruleset**                                                            |
|-------------|------------------------------------------------------------------------|
| Type        | `string`                                                               |
| Regex       | `^\s*([^\n]+)\s*$`                                                     |
| Explanation | Newline characters are not allowed. <br>Leading and trailing whitespaces should be ignored. |
---

### **key: `"description"` (optional)**
| value       | **ruleset**                                                            |
|-------------|------------------------------------------------------------------------|
| Type        | `string`                                                               |
| Default     | `NULL`                                                                 |
---

### **key: `"expiration"` (optional)** 
| value       | **ruleset**                                                            |
|-------------|------------------------------------------------------------------------|
| Type        | `integer`                                                              |
| Regex       | `^\s*(\d{1,7})\s*$`                                                    |
| Default     | 5000                                                                   |
| Explanation | <ul><li>0 &#8804; expiration &#8804; 9999999</li></lu><li>The block of the voting transaction &#8804; the block of the proposal inscription + expiration</li><li>Leading and trailing whitespaces should be ignored (To enhance understanding, it is recommended to represent the exact expiration time in the index by converting it into block height and UTC+0 time. This approach ensures greater clarity and ease of comprehension.)</li></lu> |
---

### **key: `"options"` (optional)** 
| value       | **ruleset**                                                            |
|-------------|------------------------------------------------------------------------|
| Type        | `object`                                                               |
| Default     | `NULL`                                                                 |

JSON object:
```json
{
    "option": "description"
}
```


| option      | **ruleset**                                                            |
|-------------|------------------------------------------------------------------------|
| Type        | `string`                                                               |
| Regex       | `(?i)^\s*([^\s]+)\s*$`                                                 |
| Explanation | Options are case-insensitive. There should be no whitespaces within the option, and leading or trailing whitespaces will be ignored. |                                                                  |

## Indexing ballots
| content      | **ruleset**                                                            |
|--------------|------------------------------------------------------------------------|
| Type         | `'text/plain'`                                                         |
| Regex        | `(?i)^\s*([a-f0-9]+i[0-9]+):\s*([^\s]+)\s*$`                           |
| Explanation  |  <ul><li>It is case-insensitive, and leading or trailing whitespaces should be disregarded.</li> <li>There should be no whitespaces between the inscription ID and the colon while whitespaces between the colon and the options will be ignored.</li> <li>There should be no whitespaces within the options.</li></ul> |      

## Indexing voting results
- It doesn't matter which address inscribed the ballot, but the validity of the ballot inscription should be checked.
- Only after the vote process of the ballot is completed (i.e., send the ballot to the same address ), can it be considered a valid vote. 
- In a single proposal, only the first submitted ballot from each address is counted as valid. Any subsequent duplicate votes or attempts to alter a vote should be deemed invalid.
- First is first. Use the block number and transaction index within the block to determine the voting order. However, it is important to note that there is still a possibility of multiple votes for the same proposal within a single transaction. In such instances, the earliest created ballot should be chosen as the valid vote for tallying.

## Indexing comments
1. When a proposal is deployed without options, it will be considered as a proposal aimed at collecting suggestions and feedback from the community. 
2. When certain ballots do not match any options of a proposal, those ballots will not be counted towards the proposal. 
The uncounted ballots should be regarded as comments on the proposals, which should be shown to the commnunity as well. 

