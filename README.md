# About
This repo contains CSVs of Pokemon data I use for my various projects on [PTGigi.com](https://ptgigi.com). Most of the data is official and from [PokeAPI](https://github.com/PokeAPI/pokeapi), then transformed to a format that makes more sense for my needs. Gaps in data are filled in using various official game data dumps or Pokemon fan sites such as Bulbapedia, Serebii, etc. Some of the data is custom (by me).

In general, this repo only contains current data relevent to my projects. You're welcome to use it, though I as I base the structure on my project needs, I may adjust it without warning.

## Contributions
I'm happy to take contributions to this repo, just a few notes:
* **Wrong or Missing Data**: feel free to open an issue or pull request if you spot wrong or missing data.
* **Missing Pokemon Forms**: I mostly stick to Pokemon forms from core games, with a handful from spin-offs I like. If you see something missing, feel free to ask.
* **Uncollected Data**: for data I'm not yet collecting, feel free to open an issue to ask to see if that's something I'm looking to track. If I am, contributions may be appreciated.
* **Historic/Change Log Data**: not tracking right now, sorry. I'm keeping data in this repo up-to-date with the latest game/DLC releases (preferring core games to spin-offs) and not tracking data that has changed over games.

# Table List
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

### ability
List of abilities and the generation it was introduced.

### biome
List of SV biomes.

### changeMethod
List of form change and evolution methods. This is high level and non-specific information like "when the Pokemon gains a level" and doesn't contain extra details like the specific level.

Along with `changeProcess` and `changeProcessLink`, these tables describe how a Pokemon changes form or evolves.

### changeProcess
List of form change and evolution processes. This is detailed information on how a Pokemon can evolve or change form. 

**Additional Column Info**:
`name` is just a brief descriptor.
`changeMethodId` links to the `changeMethod` table, the rest of the columns define extra variables and details.
`item` is currently a free text field, just lists the item name. I'm unlikely to add an `item` table, but if I did I'd change this to be a foreign key to that table.
`time` is a free text field. "day" and "night" are the most common, but of course there's funny stuff like "7:00-7:59PM" or "3 days" for some uniquer methods.
`move` is currently a free text field, it will eventually be renamed to `moveId` and linked to a `move` table.
`count` depends on the `changeMethod`, it could be a quantity (Meltan evo), how many times an action needs to be repeated (Primeape evo), a percentage (RNG evos like Wurmple), etc.
`regionId` and `isRegionNot` denote the region and if that region requirement is negated. Ex. Pikachu evolves into Alolan Raichu if in Alola but Kantonian Raichu if **not** in Alola.
`value` is a free text field with a specific value not covered by another field. This could be a location (Burmy form), game (Cosmoem's evo), weather (Sliggoo evo), etc.
`isUnique` means the process is very unique. I'm looking to phase out this column and move relevent information to `changeMethod` table or other columns.
`additionalInfo` is just additional descriptive text or an explanation of the unique process if `isUnique`=1.

### changeProcessLink
Links form to their evolution/new form and the process by which they change.

`pkmnFormId` is the starting form and `newPkmnFormId` is the resulting form.
`isEvolution` notes if the change is an evolution (1) or a form change (0).

### color
List of official PokeDex colors.

### contestType
List of contest type information.

### eggGroup
List of egg groups.

### formType
List of form categories. 

### friendArea
List of Friend Areas (Rescue Team Camps) from relevant PMD games.

### gender
List of genders (the 3 used by Pokemon that is).

### genderRatio
List of gender ratios.

When divided by 1000, the `value` column is the % chance of the Pokemon being female.

### generation
List of game generations.

### groupMember
Links the members of a group (pkmnForm, pkmnSpecies, or group).

### groups
List of groups.

### growthRate
List of growth rates.

### habitat
List of habitats.

### nature
List of natures.

### palParkArea
List of Pal Park Areas.

### pkmnForm
List of Pokemon forms, specifically Pokemon data that can change between a Pokemon's different forms.

Along with `pkmnSpecies` this is going to be the bulk of the data about individual Pokemon.

### pkmnSpecies
List of Pokemon species, specifically Pokemon data that does not change between a Pokemon's different forms.

### region
List of regions. 

I only add regions here only when I need that info referenced in other tables, so I don't have all regions listed.

### shape
List of PokeDex shapes. Names are unofficial.

### speciesType
List of species categories.

### type
List of types.

### typeChart
List of how Pokemon types interact with one another.
