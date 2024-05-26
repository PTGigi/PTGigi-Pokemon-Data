# About
This repo contains CSVs of Pokemon data I use for my various projects on [PTGigi.com](https://ptgigi.com). Most of the data is official and from [PokeAPI](https://github.com/PokeAPI/pokeapi), then transformed to a format that makes more sense for my needs. Gaps in data are filled in using various official game data dumps or Pokemon fan sites such as Bulbapedia, Serebii, etc. Some of the data is custom (by me).

In general, this repo only contains current data relevent to my projects. You're welcome to use it, though I as I base the structure on my project needs, I may adjust it without warning.

## Contributions
I'm happy to take contributions to this repo, just a few notes:
* **Wrong or Missing Data**: feel free to open an issue or pull request if you spot wrong or missing data.
* **Missing Pokemon Forms**: I mostly stick to Pokemon forms from core games, with a handful from spin-offs I like. If you see something missing, feel free to ask.
* **Uncollected Data**: for data I'm not yet collecting, feel free to open an issue to ask to see if that's something I'm looking to track. If I am, contributions may be appreciated.
* **Historic/Change Log Data**: not tracking right now, sorry. I'm keeping data in this repo up-to-date with the latest game/DLC releases (preferring core games to spin-offs) and not tracking data that has changed over games.

# Table Information
A list of all the tables and a brief explanation of what's in it. See below for more structural notes.

## Basic Info
If you're here for Pokemon data, 90% of what you need is in the `pkmnForm` or `pkmnSpecies` tables. These are where all the information about a Pokemon is stored. `pkmnForm` is form specific data (data that can change between forms), `pkmnSpecies` is species specific data (data that is the same for all forms of a species).

The first column in all tables is a primary key with the naming scheme `tableNameId`. If referenced in another table (foreign key) it uses this name unless referenced more than once. In that case, additional clarifiers may be added. Examples:
* in the `pkmnForm` table, the `pkmnSpeciesId` column refers to the `pkmnSpecies` table (the form's species).
* in the `pkmnForm` table, `typeId1` and `typeId2` columns reference the `type` table (`typeId1` is the form's primary type, `typeId2` is the secondary type).
* in the `groupMember` table, the `groupId` column is the group the member belongs to while `childGroupId` is used if the member is a group (all members of the child group are members of the parent group).

If I got the data from PokeAPI, I try to keep the primary keys in line with their data. But in cases I don't, so I've added a column to track the associated PokeAPI id.

"Calculated" columns are columns where I run a script to update them. These are columns that can be derived from other columns, but are useful enough to have precalculated rather than calculate at runtime in a project.  Example:
* `pkmnSpecies` table `stage` column can be calculated based on the `changeProcessLink` table (does this Pokemon have pre-evolutions according to that table).

## Table List

### ability
List of abilities.

|Column|Description|
|---|---|
|abilityId|Primary key|
|name|Name|
|generationId|Links to `generation` table; generation introduced|

### biome
List of SV biomes.

|Column|Description|
|---|---|
|biomeId|Primary key|
|name|Name|

### changeMethod
List of form change and evolution methods. This is high level and non-specific information like "when the Pokemon gains a level" and doesn't contain extra details like the specific level.

|Column|Description|
|---|---|
|changeMethodId|Primary key|
|name|Name|
|description|Description|

### changeProcess
List of form change and evolution processes. This is detailed information on how a Pokemon can evolve or change form. 

|Column|Description|
|---|---|
|changeProcessId|Primary key|
|name|Name|
|changeMethodId|Link to `changeMethod` table; the change method|
|level|Required level|
|item|Required item|
|time|Required time (time of day, duration, range)|
|abilityId|Link to `ability`; required ability|
|move|Required move; once I create a `move` table,  this will be renamed to `moveId` and link to that table|
|count|Required count/quantity/threshold/percentage/etc, what it represents depends on the change method|
|typeId|Link to `type`; required type|
|genderId|Link to `gender`; required gender|
|additionalPkmnFormId|Link to `pkmnForm`; required Pokemon|
|regionId|Link to `region`; required region|
|isRegionNot|If the region is negated; 1=not in the `regionId`, 0=in the `regionId`|
|value|Required value not covered in another column, such as location/weather/game/etc|
|isUnique|TBH I'm slowly phasing this out, it predated the `changeMethodId` column and I'd like to move unique criteria to there|
|additionalInfo|Additional information; if `isUnique`=1 then what the unique condition is|

### changeProcessLink
Links form to their evolution/new form and the process by which they change.

|Column|Description|
|---|---|
|changeProcessLinkId|Primary key|
|pkmnFormId|Link to `pkmnForm`; the base Pokemon form|
|newPkmnFormId|Link to `pkmnForm`; the new Pokemon form|
|isEvolution|If the change is an evolution or form change; 1=Evolution, 0=Form change|
|changeProcessId|Link to `changeProcessId`; the change process used|

### color
List of official PokeDex colors.

|Column|Description|
|---|---|
|colorId|Primary key|
|name|Name|

### contestType
List of contest type information.

|Column|Description|
|---|---|
|contestTypeId|Primary key|
|name|Name|
|flavor|Flavor|
|colorName|Color|

### eggGroup
List of egg groups.

|Column|Description|
|---|---|
|eggGroupId|Primary key|
|name|Name|

### formType
List of form categories. 

|Column|Description|
|---|---|
|formTypeId|Primary key|
|name|Name|

### friendArea
List of Friend Areas (Rescue Team Camps) from relevant PMD games.

|Column|Description|
|---|---|
|friendAreaId|Primary key|
|parentFriendAreaId|Link to `friendArea`; the folder/parent of this row|
|name|Name|
|isFolder|1=this is a folder/group of Friend Areas; 0=this row is a Friend Area|

### gender
List of Pokemon genders.

|Column|Description|
|---|---|
|genderId|Primary key|
|name|Name|

### genderRatio
List of gender ratios.

|Column|Description|
|---|---|
|genderRatioId|Primary key|
|name|Name|
|value|When divided by 1000, gives you the percent chance of female|

### generation
List of game generations.

|Column|Description|
|---|---|
|generationId|Primary key|
|roman|Roman numerals|
|regionId|Link to `region`, the primary region of the major core game of this generation|

### groupMember
Links the members of a group (pkmnForm, pkmnSpecies, or group).

|Column|Description|
|---|---|
|groupMemberId|Primary key|
|groupId|Link to `groupId`; group this member is a part of|
|pkmnFormId|Link to `pkmnForm`; the Pokemon form in this group|
|pkmnSpeciesId|Link to `pkmnSpecies`; the Pokemon species in this group, shorthand for all forms of a species are in this group|
|childGroupId|Link to `group`; the group in this group, shorthand for all members of the child group are in this group|

### groups
List of groups.

|Column|Description|
|---|---|
|groupId|Primary key|
|name|Name|
|type|How group members are added; "family" means all members of a Pokemon's family are included|
|sort|Preferred sort order: alphabetical by category then PokeDex order|

### growthRate
List of growth rates.

|Column|Description|
|---|---|
|growthRateId|Primary key|
|name|Name|

### habitat
List of habitats.

|Column|Description|
|---|---|
|habitatId|Primary key|
|name|Name|

### nature
List of natures.

|Column|Description|
|---|---|
|natureId|Primary key|
|name|Name|

### palParkArea
List of Pal Park Areas.

|Column|Description|
|---|---|
|palParkAreaId|Primary key|
|name|Name|

### pkmnForm
List of Pokemon forms, specifically data that can change between a Pokemon's different forms.

|Column|Description|
|---|---|
|pkmnFormId|Primary key|
|pkmnSpeciesId|Link to `pkmnSpecies`; the form's species|
|orderNum|Sort order, relative to the species' other forms|
|name|Full name|
|formName|Form name|
|formTypeId|Link to `formType`; form category|
|regionId|Link to `region`; region if a regional form|
|isDefault|1=this form is the species's default form|
|isBattleOnly|1=this form is only available during battler|
|isCosmetic|1=this form has only cosmetic changes; other stats should be identical|
|isEvent|1=this form is only available via official events|
|isSexuallyDimorphic|1=this form has gender differences, either this is a gender-specific form or different genders have different appearances|
|isSpecial|1=this form is non-standard: glitch, legacy, unavailable, etc.|
|typeId1|Link to `type`; primary type|
|typeId2|Link to `type`; secondary type|
|hp|Base HP stat|
|att|Base Attack stat|
|def|Base Defense stat|
|spAtt|Base Special Attack stat|
|spDef|Base Special Defense stat|
|spd|Base Speed|
|specialStat|Base Special Stat, from RBGY|
|height|Height, divide by 10 to get meters|
|weight|Weight, divide by 10 to get kg|
|generationId|Link to `generation`; generation introduced|
|genderRatioId|Link to `genderRatio`; form's gender ratio|
|colorId|Link to `color`; official PokeDex color|
|shapeId|Link to `shape`; PokeDex shape|
|abilityId1|Link to `ability`; primary ability|
|abilityId2|Link to `ability`; secondary ability|
|abilityIdHidden|Link to `ability`; hidden ability|
|eggGroupId1|Link to `eggGroup`; primary egg group|
|eggGroupId2|Link to `eggGroup`; secondary egg group|
|category|PokeDex category|
|friendship|Base friendship|
|exp|Base experience yield|
|hpEVs|Base HP EV yield|
|attEVs|Base Attack EV yield|
|defEVs|Base Defense EV yield|
|spAttEVs|Base Special Attack EV yield|
|spDefEVs|Base Special Defense EV yield|
|spdEVs|Base Speed EV yield|
|friendAreaId|Link to `friendArea`; PMD Friend Area/Rescue Camp|
|isGigantic|If in the PMD games this Pokemon takes up multiple tiles|
|pSpeed|Performance Stat: Speed (Min, Base, Max)|
|pPower|Performance Stat: Power (Min, Base, Max)|
|pSkill|Performance Stat: Skill (Min, Base, Max)|
|pStamina|Performance Stat: Stamina (Min, Base, Max)|
|pJump|Performance Stat: Jump (Min, Base, Max)|
|pokeAPIId|Link to PokeAPI's `pokemon_forms` table|
|pokeAPIPokemonId|Link to PokeAPI's `pokemon` table|

### pkmnSpecies
List of Pokemon species, specifically data that does not change between a Pokemon's different forms.

|Column|Description|
|---|---|
|pkmnSpeciesId|Primary key|
|orderNum|Sort order, groups a family together|
|name|Name|
|baseCatchRate|Base catch rate|
|baseEggCycle|Base egg cycle|
|generationId|Link to `generation`; generation introduced|
|genderRatioId|Link to `genderRatio`; species's gender ratio|
|growthRateId|Link to `growthRate`; growth rate|
|speciesTypeId1|Link to `speciesType`; species category|
|speciesTypeId2|Link to `speciesType`; another species category because I didn't feel like making a one-to-many linking table for this|
|habitatId|Link to `habitat`; FRLG habitat|
|palParkAreaId|Link to `palParkArea`; HGSS/DPPt Pal Park area|
|IQGroup|PMD:TDS IQ group|
|bodySize|PMD: TDS body size|
|recruitChance1|Main PMD recruitment chance; divide by 1000 for % chance|
|recruitChance2|Alternate PMD recruitment chance; divide by 1000 for % chance|
|recruitChance3|Shiny PMD: DX recruitment chance; divide by 1000 for % chance|
|stage|Calculated; stage|
|familySpeciesId|Calculated; link to `pkmnSpecies`; the first stage of this species|
|formCount|Calculated; number of forms|
|evolutionCount|Calculated; number of evolutions|
|preEvolutionCount|Calculated; number of pre-evolutions|

### region
List of regions. I only add regions here only when I need that info referenced in other tables, so I don't have all regions listed.

|Column|Description|
|---|---|
|regionId|Primary key|
|name|Name|
|demonym|Fancy word for what you'd call someone from this region; i.e. "Kantonian"|
|isCore|Is this a core game region|

### shape
List of PokeDex shapes.

|Column|Description|
|---|---|
|shapeId|Primary key|
|name|Unofficial name|

### speciesType
List of species categories.

|Column|Description|
|---|---|
|speciesTypeId|Primary key|
|name|Name|

### type
List of types.

|Column|Description|
|---|---|
|typeId|Primary key|
|pokeAPIId|Link to PokeAPI `types` table|
|name|Name|
|hexColor|Official hex color|
|isCore|If this is one of the main 18 types|
|pureCount|Calculated; count of types per forms; +1 per type|
|weightedCount|Calculated; count of types per forms; +1 if mono-typed form, +0.5 if dual-typed form|
|pureSpeciesCount|Calculated; like pureCount but weighed for a species|
|weightedSpeciesCount|Calculated; like weightedCount but weighed for a species|

### typeChart
List of how Pokemon types interact with one another.

|Column|Description|
|---|---|
|typeChartId|Primary key|
|attTypeId|Link to `type`; attacking type|
|defTypeId|Link to `type`; defending type|
|modifier|Modifier|
