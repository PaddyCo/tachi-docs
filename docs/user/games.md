# Games

Tachi supports multiple games at the same time. It does
this using Games and Playtypes.

*****

## What's a playtype?

A playtype is a 'sub game' for a given game. For example,
DDR has two 'playtypes', one where the player plays on 4
panels, and one where the player plays on 8 panels.

For DDR, these are referred to as SP and DP - corresponding
to Single Play and Double Play.

Games **MUST** have playtypes, even if the game only has
one practical playtype!

For this scenario, the playtype `Single` is used, so for
something like SDVX, it would be SDVX Single.

### Why?

Playtypes are for separating different scores on the same
game, we don't want to get a user's DP scores mixed with
their SP scores as, for all intents and purposes, they are
completely separate games that need completely different
skills!

As for why games **MUST** have playtypes, it's generally
for futureproofing, and it works nicely with the codebase.

For games that don't *really* have multiple playtypes, this
fact is hidden in the UI, but might still poke up in URLs.

## What games and playtypes are supported?

### Kamaitachi

The following games are supported on [Kamaitachi](https://kamaitachi.xyz).

| Name | Internal Name | Playtypes |
| :: | :: | :: |
| beatmania IIDX | `iidx` | `SP`, `DP` |
| MÚSECA | `museca` | `Single` |
| maimai | `maimai` | `Single` |
| SOUND VOLTEX | `sdvx` | `Single` |
| Dance Dance Revolution | `ddr` | `SP`, `DP` |
| CHUNITHM | `chunithm` | `Single` |
| GITADORA | `gitadora` | `Gita`, `Dora` |

### Bokutachi

The following games are supported on [Bokutachi](https://bokutachi.xyz).

| Name | Internal Name | Playtypes |
| :: | :: | :: |
| BMS | `bms` | `7K`, `14K` |
| unnamed_sdvx_clone | `usc` | `Single` |

<!-- | jubeat | `jubeat` | `Single` | -->
<!-- | pop'n music | `popn` | `9B`[^1] | -->

<!-- [^1]: Pop'n *had* some other playtypes, namely a 5-Button mode, but it seems to be removed now. 9B is used instead of single here for futureproofing. -->