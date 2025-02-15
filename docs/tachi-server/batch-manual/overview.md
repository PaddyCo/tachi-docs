# What is BATCH-MANUAL?

BATCH-MANUAL is a JSON format that Tachi accepts.
This format can be submitted as [a file](../../api/routes/import.md#import-scores-from-a-file)
using the `file/batch-manual` [Import Type](../import/import-types.md), or it can be submitted as a
[HTTP request body](todo).

*****

## Motivation

Instead of Tachi writing new support for every kind of
possible export, and bothering other service providers
to write exports, we could write a generic format we accept
and then users with a bit of scripting knowledge can import
their own scores.

This has the additional advantage of allowing extremely
obscure imports, and reduces the workload on Tachi.

## Format

The format is incredibly simple JSON.

It is comprised of two base keys, `meta` and `scores`.

!!! note
	These keys were originally called `head` and `body` in Kamaitachi. You will have to update
	existing batch-manual code.

### Meta

The `meta` key contains metadata, and looks like this:

```json
{
	"game": "iidx",
	"playtype": "SP",
	"service": "foobar"
}
```

The fields have the following values:

| Property | Type | Description |
| :: | :: | :: |
| `game` | Any Game Identifier | The game this import is for. |
| `playtype` | Any Playtype for the above game. | The playtype this import is for. |
| `service` | string | A humanised string to explain where these scores are from. This must be between 2 and 15 characters. |
| `version` (Optional) | string | Optionally, you can specify a version of the game this import is for. This should be used when conflicting versions of songs exist, or when this import is rather old. Most of the time, this does not need to be present. |

### Scores

The `scores` property is an array of Batch Manual Scores. An example
score is as follows:

```json
{
	"score": 500,
	"lamp": "HARD CLEAR",
	"matchType": "songTitle",
	"identifier": "5.1.1.",
	"difficulty": "ANOTHER",
	"timeAchieved": 1624324467489
}
```

The properties are described as this:

| Property | Type | Description |
| :: | :: | :: |
| `score` | Number | The score for this, well, score. This should use the default scoring algorithm for this game. |
| `lamp` | Lamp | The lamp for this score. This should be one of the lamps as described in the config for your game + playtype. |
| `matchType` | "songTitle" \| "ddrSongHash" \| "tachiSongID" \| "bmsChartHash" \| "inGameID" \| "uscChartHash" | This determines how `identifier` will be used to match your scores' chart with Tachi's database of songs and charts. |
| `identifier` | string | A string that Tachi uses to identify what chart this is for. How this is used depends on the `matchType`. |
| `difficulty` (Conditional) | string | If `matchType` is "tachiSongID", "inGameID", "ddrSongHash" or "songTitle", this field must be present, and describe the difficulty of the chart this score is for. |
| `timeAchieved` (Optional) | integer \| null | This is *when* the score was achieved in unix milliseconds. This should be provided if possible, as Tachi uses it for a LOT of features. |
| `comment` (Optional) | string \| null | A comment from the user about this score. |
| `judgements` (Optional) | Record&lt;Game Judgement, integer&gt; | This should be a record of the judgements for your game + playtype, and the integer indicating how often they occured. |
| `hitMeta` (Optional) | See [Game Specific Hit Meta](../documents/score.md#game-specific) | This can be a partial record of various `hitMeta` props for this game. |
| `scoreMeta` (Optional) | See [Game Specific Score Meta](../documents/score.md#game-specific) | This can be a partial record of various `scoreMeta` props for this game. |

!!! warning
	`identifier` should always be a string. Even if it's something like a numeric ID! Tachi will handle this.

!!! warning
	`timeAchieved` is in **UNIX MILLISECONDS**. Most programming languages use unix seconds. You might have to
	multiply your timestamps by 1000.

#### Match Type

There are five match types, and they all use identifier
in a different way.

- songTitle

As the name implies, this searches for a song who's title
resembles `identifier`. **THIS IS NOT FUZZY MATCHING**,
and is by far the least reliable way to send scores to
Tachi. This is kept for compatibility purposes with poor quality APIs.

This match type *necessitates* that `difficulty` be defined
and set to a valid difficulty for this game + playtype.

- tachiSongID

This uses `identifier` as if it were an integer, to match
songs based on the `id` field of a tachi song.

This match type *necessitates* that `difficulty` be defined
and set to a valid difficulty for this game + playtype.

- bmsChartHash

As the name implies, this looks for the chart hash BMS
uses. This can be either the MD5 hash or the SHA256 hash,
both will match.

This match type can only be used for BMS.

- uscChartHash

This looks for the chart SHA1 that USC uses. As expected, this
can only be used for USC.

- ddrSongHash

This looks for the DDR 'song hash'. This is an identifier
used on the e-amusement website, and references a song,
not a chart. As such:
This match type *necessitates* that `difficulty` be defined
and set to a valid difficulty for DDR.

This match type can only be used for DDR.

- inGameID

This uses the in-game-ID for this **SONG**. You, therefore,
**MUST** specify the difficulty for this chart aswell.

At the moment, this match type can only be used on `iidx`.
This is expected to change before release.

## Example

A final example of a simple BATCH MANUAL format
looks like this:

```json
{
	"meta": {
		"game": "iidx",
		"playtype": "SP",
		"service": "My Service"
	},
	"scores": [{
		"score": 500,
		"lamp": "HARD CLEAR",
		"matchType": "songTitle",
		"identifier": "5.1.1.",
		"difficulty": "ANOTHER"
	}, {
		"score": 123,
		"lamp": "FAILED",
		"matchType": "tachiSongID",
		"identifier": "1",
		"difficulty": "HYPER",
		"comment": "This score sucked!",
		"hitMeta": {
			"bp": 5
		},
		"scoreMeta": {
			"random": "MIRROR"
		}
	}]
}
```