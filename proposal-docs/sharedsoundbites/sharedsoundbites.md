# The Shared SoundBites Specification

<small>Version 0.1 by John Chidgey - 2022.07.03</small>

<br>

## Purpose

SoundBites to date have been placed in the podcast source of truth: the RSS feed. Whether they remain embedded in the XML or converted into a referenced JSON file (as per PC2.0 Chapters) as a potential future evolution should be considered to reduce potential feed bloat/hosting bandwidth considerations. The podcaster controls and owns their RSS feed (or should) and as such controls the flow of value and approval of that feeds' content.

SoundBites remain a burden to the podcaster to create and embed in their feed. Traditionally SoundBites have been shared via applications, encoded as their own audio file and attached/posted to a social media platform, or via embedding them in a website and shared as a link. All existing methods do not allow the podcast creator to easily import them in their RSS feed, nor to reward those that created them (the Soundbiters).

To accomplish this, a standard SoundBite sharing format could permit sharing in a simple form that can be imported by the podcaster using local tools or easily added to hosting provider platforms, as well as played in any web/device app. Such a format should contain the existing elements of the SoundBite tag, but also reference the audio file and value address for V4V.

In this way the podcaster could present a new incentivisation pathway for V4V redistribution, with any playback of the SoundBite they incorporate into the RSS feed getting a set split of streaming value of an agreed fixed limit between the SoundBiter and the podcaster. Note that per minute compensation is not as useful as clips vary in length and per-minute clips would incentivise poor Soundbiter behaviour.

## Requirements

Audio only, Constant BitRate encoded audio file.

## Implementation Challenges

The onus on playhead position and duration for audio/video remains on the client application, and if the podcaster chooses a non-linear format (eg VBR encoding) then playhead position and duration can be difficult to determine. There are many video formats in use which could be problematic, hence restricting this to Audio/MPEG3 at Constant BitRate (CBR) is the best place to start as a requirement for use in this standard.

The podcaster is therefore required to specify the exact MP3 to use to ensure all Soundbiters are using the correct file for Soundbiting and discerning this from the feed where an <alternateEnclosure> has been used is difficult to codify. In some cases the podcaster may choose to specify a different MP3 than the default <enclosure> MP3, as this MP3 is often the reference file for statistics that would be inflated significantly if that specific file was used for play/scrub to position by Soundbiters and clients playing final Soundbites.

## SoundBites Tag

Group soundbites under a new <podcast:soundbites> element, with each soundbite being an individual element beneath that, with the option to use JSON (per the Chapters standard). With the introduction of the alternate enclosure tag and to allow editing without needing to query the RSS feed item directly, linking to a source audio file of truth will reduce ambiguity and guarantee timestamps are correctly aligned.

#### Parent
`<item>`

#### Count
Single

#### Attributes
 - **open (OPTIONAL)**: Default = TRUE. Boolean: If TRUE, open to accepting soundbite submissions for this item/episode. If FALSE, any submissions will be rejected.
 - **split (OPTIONAL):** The number of shares of the payment this recipient will receive, hence 50 = equal amount to podcaster and to soundbiter, 1 all to soundbiter, 0 all to podcaster.
 - **url (OPTIONAL):** The URL where the soundbites file is located.
 - **type (OPTIONAL):** Mime type of file - JSON preferred, 'application/json+soundbites'.

#### Examples
`<podcast:soundbites open="true" split="50" url="https://example.com/episode1/soundbites.json" type="application/json+soundbites" />`

## The episode is open for submissions, however none currently exist or have been accepted into the feed

`<podcast:soundbites open="true" split="50" />`

<br><br>

### Soundbites Element

The `valueRecipient` tag designates various destinations for payments to be sent to during consumption of the enclosed.

#### Parent
`<podcast:soundbites>`

#### Count
Multiple

#### Attributes
 - **startTime:** (SAME AS EXISTING <podcast:soundbite> IMPLEMENTATION) The time where the soundbite begins
 - **duration:** (SAME AS EXISTING <podcast:soundbite> IMPLEMENTATION) How long is the soundbite (recommended between 15 and 120 seconds)
 - **title:** (Now required, was a node value now a named attribute) Used as free form string from the podcast creator to specify a title for the soundbite. Please do not exceed `128 characters` for the title value or it may be truncated by aggregators.
 - **url**: Source Audio file URL

## Value Attributes

 - **name (OPTIONAL):** A free-form string that designates who or what this recipient is.
 - **type (OPTIONAL/REQUIRED):** This is the service slug of the cryptocurrency or protocol layer hat represents the type of receiving address that will receive the payment.
 - **method (OPTIONAL):** This is the transport mechanism that will be used. (Note: Aligned with <podcast:value>)
 - **address (OPTIONAL/REQUIRED):** This denotes the receiving address of the payee.
 - **customKey (OPTIONAL):** The name of a custom record key to send along with the payment.
 - **customValue (OPTIONAL):** A custom value to pass along with the payment. This is considered the value that belongs to the `customKey`.

#### Value notes:

The value tag attribute: 'split' is defined at the top level and should be equal for all soundBites in a given feed, which is set by the Podcaster. Both the Podcaster(s) and the SoundBiter should be compensated for their effort and hence a 50/50 split (0.5) should be the default.

The fee is a per play amount before the split and is derived from the podcast:value tags `suggested` attribute, applied over the recommended maximum duration of a soundbite (nominally 120 seconds). For a client to process a soundbite value split therefore, it is mandatory to have a podcast:value block defined as well.

  TBD 1: For Lightning, 1 sat/min streaming rate, 50/50 split is only 1 sat/soundbite which is too small to route. So we consider a 10x multipler by default to avoid this? How much do we value Soundbiters - I think that's probably fair?)
  TBD 2: Which is the Podcaster true recipient? In a multi-host podcast there could be multiple therefore perhaps an split between all parties defined in the value block for this item.

There is nothing stopping a podcaster from changing this split after multiple Soundbites have been created however those creating the Soundbite that are motivated by this would observe no value from that effort and would stop contributing in future in that was the case.

Value attributes are all optional, however if they are to be used, optional/required indicates they are required if value is to be used. Some Soundbiters may be happy with name attribution, no attribution whatsoever and not have a streaming value destination available to them.

#### XML Examples

`<podcast:soundbites split="50" url="https://example.com/episode1/soundbites.json" type="application/json+soundbites" />`

```
<podcast:soundbites split="50">
  <podcast:soundbite
    startTime="1234.5"
    duration="42.25"
    title="Why the Podcast Namespace Matters"
    url="https://somewhere.hostingplace.com/ashow/E001-anEpisode.mp3"
    name="A Soundbiter"
    type="node"
    address="02d5c1bf8b940dc9cadca86d1b0a3c37fbe39cee4c7e839e33bef9174531d27f52"
    customKey="[optional key to pass(mixed)]"
    customValue="[optional value to pass(mixed)]"
  />
  <podcast:soundbite
    startTime="134.5"
    duration="30.0"
    title="Why Soundbites Matter"
    url="https://somewhere.hostingplace.com/ashow/E001-anEpisode.mp3"
    name="A Soundbiter again"
    type="node"
    address="02d5c1bf8b940dc9cadca86d1b0a3c37fbe39cee4c7e839e33bef9174531d27f52"
    customKey="[optional key to pass(mixed)]"
    customValue="[optional value to pass(mixed)]"
  />
</podcast:soundbites>
```

#### JSON Example

```
{
	"version" : "1.0",
	"soundbites" :
	[
		{
      "startTime" : "1234.5",
      "duration" : "42.25",
      "title" : "Why the Podcast Namespace Matters",
      "url" : "https://somewhere.hostingplace.com/ashow/E001-anEpisode.mp3",
      "name" : "A Soundbiter",
      "type" : "node",
      "address" : "02d5c1bf8b940dc9cadca86d1b0a3c37fbe39cee4c7e839e33bef9174531d27f52",
      "customKey" : "[optional key to pass(mixed)]",
      "customValue" : "[optional value to pass(mixed)]"
		},
    {
      "startTime" : "134.5",
      "duration" : "30.0",
      "title" : "Why Soundbites Matter",
      "url" : "https://somewhere.hostingplace.com/ashow/E001-anEpisode.mp3",
      "name" : "A Soundbiter again",
      "type" : "node",
      "address" : "02d5c1bf8b940dc9cadca86d1b0a3c37fbe39cee4c7e839e33bef9174531d27f52",
      "customKey" : "[optional key to pass(mixed)]",
      "customValue" : "[optional value to pass(mixed)]"
    }
	]
}
```

## Soundbite Sharing Specification

Sharing a soundbite has to be easy for client or web applications to implement and for podcast hosts or podcast servers to ingest with tools to add/insert into the RSS/JSON for the source of truth RSS Feed.

Primary method suggested for sharing is via a JSON File. Only one soundbite may be shared per file/string. Each soundbite will be processed individually.

#### JSON Sharing File Example

```
{
  "startTime" : "134.5",
  "duration" : "30.0",
  "title" : "Why Soundbites Matter",
  "url" : "https://somewhere.hostingplace.com/ashow/E001-anEpisode.mp3",
  "name" : "A Soundbiter again",
  "type" : "node",
  "address" : "02d5c1bf8b940dc9cadca86d1b0a3c37fbe39cee4c7e839e33bef9174531d27f52",
  "customKey" : "[optional key to pass(mixed)]",
  "customValue" : "[optional value to pass(mixed)]"
}
```

#### Server Component (Soundbitten)

Written in NodeJS. Accepts API calls from a client, presents the soundbites for a podcaster to select from, download the ones you want to keep and name them in JSON file for inclusion/import into RSS feed.

Code here: https://github.com/johnchidgey/soundbittennode
Example site here: https://johnchidgey.github.io/

#### Client Component (Soundbiter Client)

Written in Javascript to run in the browser as a Client application to create Soundbites.

Code here: https://johnchidgey.github.io/createsoundbites.html
Example site here: https://github.com/johnchidgey/johnchidgey.github.io

#### Client Component (Manage Soundbites Client)

Written in Javascript to run in the browser as a Client application to Manage Soundbites. Sends API call to Soundbitten server via a secure WebSocket.

Code here: https://johnchidgey.github.io/managesoundbites.html
Example site here: https://github.com/johnchidgey/johnchidgey.github.io

#### Notes

Discussion here:
- https://github.com/Podcastindex-org/podcast-namespace/issues/61
