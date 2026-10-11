# Privileges Framework — Echelon

Adapter catalogue for **0.3.0-beta.1**, RimWorld **1.6**. [Steam Workshop](https://steamcommunity.com/sharedfiles/filedetails/?id=3817877651).

This release includes **18 shared providers: 13 built into Core and 5 optional providers**. Sixteen providers are new in 0.3; the Vehicle Framework and Mechanoid Mechanitor providers are retained. Shared providers use loaded definitions and the game or originating framework’s public lifecycle APIs, so a separate DLL for each content mod is unnecessary. Core requires no DLC, Harmony or third-party mod. Optional features require their corresponding dependencies.

## How to use an adapter

1. Enable the content mod and its dependencies. Load Privileges Framework after the corresponding framework, VPE and Harmony when using optional integrations. Restart RimWorld.
2. Open **Options → Mod Settings → Privileges Framework → Privileges Studio → Effects library**.
3. Create an effect, choose **Adapter** and select the provider capability. Choose a loaded content definition in the additional picker. The content catalogue also selects the appropriate provider when an entry supplies adapter metadata.
4. Configure Value, Quantity and the reward lifetime. Use one-time rewards for permanent grants; only providers with a supported removal lifecycle accept continuous passives.
5. Add the effect to a privilege, case or track. For a manual action, buy the privilege, select its owner, open **Privileges** and press **Use**. Purchasing or renewing does not automatically issue manual rewards.

Validation depends on the actual pawn, map, caravan and installed definitions. A displayed adapter route is not a promise that every definition can be awarded in every situation.

## Complete provider list

| Provider ID | Capabilities | Content | Dependencies | Behavior |
| --- | --- | --- | --- | --- |
| native.genes | grant | GeneDef, including subclasses | Biotech | Xenogene insertion through AddGene; borrowed genes are preserved; exact owned receipts can expire. |
| native.health | grant | HediffDef, including custom classes and components | None for ordinary health content | HediffMaker + AddHediff; positive severity; exact-object removal. Stateful trackers and body-part changes use dedicated workflows. |
| native.abilities | grant | RimWorld AbilityDef subclasses | Depends on the loaded ability | GainAbility / RemoveAbility on the native pawn tracker; existing abilities are preserved. |
| native.pawns | grant | Non-mechanoid PawnKindDef and Pawn subclasses | Depends on the selected race | PawnGenerator + GenSpawn. Permanent delivery to a loaded map. |
| native.mechs | grant | Mechanoid PawnKindDef | Biotech | Validates free bandwidth and native control acceptance; establishes overseer relation and control group. |
| native.conditions | grant | GameConditionDef subclasses | Depends on the selected condition | Native map or world condition manager; recorded conditions can expire. |
| native.quests | grant | Selectable QuestScriptDef roots | Depends on the selected quest | Native CanRun, QuestUtility generation and quest letter; helper roots are excluded. |
| native.incidents | grant | IncidentDef targeting maps, the world or a caravan | Depends on the selected incident | TargetAllowed, CanFireNow and TryExecute; caravan selection must be unambiguous. |
| native.traits | grant | TraitDef | None | Native GainTrait; degree and trait conflicts are validated. Permanent gift. |
| native.mental_states | grant | MentalStateDef | Depends on the selected state | Native mental-state transition; native worker can reject it. One-time action. |
| native.memories | grant | ThoughtDef memory thoughts | Depends on the selected thought | ThoughtMaker + TryGainMemory and native thought eligibility; social memories needing another pawn are excluded. |
| native.backstories | grant | BackstoryDef | Depends on the selected story | Updates the matching childhood/adulthood slot and disabled-work cache. Permanent change. |
| native.surgery | install | RecipeDef for artificial parts and implants | Depends on the selected recipe | Runs the real installation recipe worker. Value selects a zero-based eligible body-part index. Guaranteed gift without a surgeon success roll; irreversible. |
| vef.abilities | grant | VEF AbilityDef | Vanilla Expanded Framework | CompAbilities.GiveAbility with native trait/hediff prerequisites. Permanent only; use vpe.psycasts for psycasts. |
| vef.outposts | create, deliver | Outpost WorldObjectDef / item ThingDef | Vanilla Expanded Framework; loaded outpost content | Create from the owner’s unique eligible player caravan through WorldObjectMaker and Outpost.AddPawn; includes construction costs. Deliver items through native outpost inventory. |
| vpe.psycasts | grant, experience, unlock_path, psylink | VPE abilities, paths and saved psycast tracker | Vanilla Psycasts Expanded, VEF, Royalty | Native psylink initialization, GainExperience, UnlockPath and GiveAbility. Grant checks level, path and prerequisites. Permanent rewards. |
| vehicles | move_factor, flight_factor, fuel_factor, summon | Vehicle Framework VehicleDef | Vehicle Framework and its dependencies | Speed/fuel modifiers and real vehicle generation. Select an actual loaded vehicle model. |
| mechanitor | promote_acquired, chip_bandwidth | Mechanoid Mechanitor saved role/record system | Mechanoid Mechanitor, Biotech | Native role utilities and read-only record/native/acquired requirements. Permanent role/chip gifts. |

For native grants, Alternative contains the selected definition’s defName. Value is health severity, trait degree, experience amount, psylink increment or surgery body-part index where applicable. For outpost item delivery, Alternative is an item defName and Quantity is the item count. Existing normal effects automatically route supported custom genes, health, abilities, pawns, conditions, quest roots and non-map incidents to the appropriate native provider.

## What the scan establishes

The installation audit on **11 October 2026** examined **1,095 mod folders / unique package IDs**: 1,088 third-party packages, six official packages and this framework. It read 31,163 XML files, 1,278 selected assemblies and 119,816 selected definitions. The audit was made against version 0.2 to identify gaps addressed by this update. Inactive DLLs were inspected as metadata and were not executed.

| Original audit result | Packages | Meaning |
| --- | ---: | --- |
| Adapter required | 273 | At least one operation was blocked by the former generic guards. The catalogue below maps these packages to the new shared routes. |
| Shared adapter | 8 | Vehicle content uses the existing Vehicle Framework provider. |
| Existing adapter | 2 | Vehicle Framework and Mechanoid Mechanitor integrations already existed. |
| Adapter candidate | 152 | A custom system or additional category may need an integration if offered as a reward. This is not a confirmed incompatibility. |
| Manual review required | 122 | Conditional patches or unresolved content require inspection of the actual loaded configuration. |
| Generic support | 127 | Ordinary definitions matched generic operations in the static audit. |
| No direct integration | 404 | No directly relevant reward operation was found. |
| Official baseline / framework | 7 | Six official packages and this framework. |

The 273 required packages are **not 273 individually tested adapters**. One package can use several providers, and a provider can serve many packages. Definition inheritance, conditional LoadFolders and the active patch stack are resolved by RimWorld at runtime. A mod without a declared 1.6 version still requires a version compatibility review.

## Validation of this release

| Check suite | Result |
| --- | --- |
| Domain/data tests | 69 passed |
| Real game, Core only, no DLC | 104 passed, including real save/load |
| Real game, all five DLC plus representative content integrations | 154 passed, including real save/load |
| Legacy-save schema migration | 14 passed across legacy load and migrated-save reload |

Tested Core SHA-256: `C69A979FD8A76B4C0C8BC486932A3BC03C442181D8B323CB1F3E1FAFC534B0F3`. Tested optional VEF assembly SHA-256: `F3AD0CD4C39341C9CF2B1C135B08C8338CE4AAC4B06D51A66C378B3A9309AAB1`. Optional adapters are loaded only with their active dependencies. Tests used an isolated profile; the player’s active mod configuration was preserved.

Runtime checks exercise actual mutations and real save/load. Representative integrations include Alpha Genes, Rimatomics, Combat Extended, Vanilla Genetics Expanded, VPE, Vanilla Outposts Expanded, Vehicle Framework and Mechanoid Mechanitor. Results describe the tested configuration; the audit does not certify a simultaneous 1,095-mod playthrough or every definition of each listed mod. Mental-state transitions and arbitrary mod quest roots are validated through native acceptance APIs but are not independently certified by these representative checks.

## Deliberate limits

- Health grants do not insert pregnancy/addiction state, psylink trackers, the framework’s managed trackers or another ability system’s initialization hediff directly. Use the originating workflow. Supported artificial parts and implants use native.surgery; missing-part edits and arbitrary surgery workers are excluded.
- VEF and VPE rewards are permanent because the inspected public lifecycle does not provide safe general removal. Psycast grants preserve required levels, unlocked paths and prerequisite abilities. Native genes, health, abilities and conditions support removal through recorded ownership.
- Only independent, randomly selectable quest roots can be generated. Helper roots and quests requiring additional undisclosed Slate inputs may be rejected by native CanRun.
- Mental states must be accepted by the native handler. Memories must be eligible memory thoughts; social memories needing another pawn are excluded. Trait conflicts and unavailable pawn trackers are rejected. Backstory changes are permanent.
- Outpost creation requires the owner in a unique eligible player caravan, a valid unoccupied tile and native pawn/skill/biome acceptance. The reward includes construction materials and uses native resident transfer. Only Outpost subclasses are supported; arbitrary WorldObjectDef creation is excluded.
- Vehicle trip-completion rewards, arbitrary building actions, advanced mechanitor workflows and unknown custom Def/tracker systems remain outside the implemented operations. The mechanitor provider offers the listed role/chip capabilities only.
- Continuous effects remove recorded framework-owned objects. Pre-existing genes/abilities and unrelated health objects are preserved; overlapping framework sources retain shared objects until the last relevant source expires. Irreversible rewards cannot be undone by privilege expiry.
- A partially completed foreign lifecycle can require review. The framework preserves execution receipts and its execution lock instead of automatically repeating an uncertain gift.

Saved framework schema remains **2**. Version 0.1 data migrates without replaying old automatic privilege orders. Keep a colony backup before upgrading; schema 2 is not intended for rollback to 0.1.

## All packages with confirmed adapter findings

The following **283 packages** comprise all 273 adapter-required packages, eight shared-adapter packages and two existing-provider packages from the audit. Routes are implementation mappings for relevant operations, subject to the limits above. Package IDs are retained exactly as declared by the originating mod. Names may retain their original language to identify them accurately.

| Mod | Package ID | Provider routes | Declares RimWorld 1.6 |
| --- | --- | --- | --- |
| [[Aya]Zoichor Race](https://steamcommunity.com/sharedfiles/filedetails/?id=3477749439) | Ayameduki.HARZoichor | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [[B.E.P Race]B.E.P Race Core](https://steamcommunity.com/sharedfiles/filedetails/?id=2975722341) | bep.race.core | native.genes; native.health; native.surgery | Yes |
| [[MAP]机械族机械师 Mechanoid Mechanitor (1.6)](https://steamcommunity.com/sharedfiles/filedetails/?id=3811415429) | xyzf.mechanoidmechanitor | mechanitor; native.backstories; native.health; native.mechs; native.memories (memory thoughts only); native.pawns; native.surgery; vef.outposts (Outpost subclasses only) | Yes |
| [[RH2] Faction: Bounty Hunters](https://steamcommunity.com/sharedfiles/filedetails/?id=2889605556) | RH2.Faction.Bounty.Hunters | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [[RH2] Faction: Gruppa Krovi](https://steamcommunity.com/sharedfiles/filedetails/?id=3221155630) | RH2.Faction.Gruppa.Krovi | native.backstories; native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [[RH2] Faction: Task Force 141](https://steamcommunity.com/sharedfiles/filedetails/?id=2694371667) | RH2.Faction.Task.Force.141 | native.backstories; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [[RH2] Faction: The Rangers](https://steamcommunity.com/sharedfiles/filedetails/?id=2694372968) | RH2.Faction.Rangers | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [[RH2] Hand-To-Hand](https://steamcommunity.com/sharedfiles/filedetails/?id=2556532381) | RH2.HandToHand | native.health; native.surgery | Yes |
| [[SRC]Miho,Star Ring Corporation](https://steamcommunity.com/sharedfiles/filedetails/?id=3534748687) | src.Core.markeazzyh | native.abilities; native.conditions; native.health; native.incidents; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Admarian, People of desert Xenotypes and Faction](https://steamcommunity.com/sharedfiles/filedetails/?id=3603781980) | Resurrectionem.Admarian | native.backstories; native.genes; native.memories (memory thoughts only) | Yes |
| [Advanced Mechanoids](https://steamcommunity.com/sharedfiles/filedetails/?id=3271746828) | Sobb.AdvancedMechanoids | native.health; native.mechs; native.surgery | Yes |
| [AF · Mech Fox](https://steamcommunity.com/sharedfiles/filedetails/?id=3015998297) | Amli.Story.MechFox | native.mechs | Yes |
| [Aliaile Race](https://steamcommunity.com/sharedfiles/filedetails/?id=3605570313) | misstalls.AliaileRace | native.abilities; native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Alpha Animals](https://steamcommunity.com/sharedfiles/filedetails/?id=1541721856) | sarg.alphaanimals | native.health; native.memories (memory thoughts only); native.mental_states; native.pawns; native.surgery; vef.abilities; vpe.psycasts | Yes |
| [Alpha Biomes](https://steamcommunity.com/sharedfiles/filedetails/?id=1841354677) | sarg.alphabiomes | native.conditions; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Alpha Books](https://steamcommunity.com/sharedfiles/filedetails/?id=3403180654) | sarg.alphabooks | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Alpha Crafts](https://steamcommunity.com/sharedfiles/filedetails/?id=3382446150) | sarg.alphacrafts | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Alpha Genes](https://steamcommunity.com/sharedfiles/filedetails/?id=2891845502) | sarg.alphagenes | native.abilities; native.genes; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits | Yes |
| [Alpha Implants](https://steamcommunity.com/sharedfiles/filedetails/?id=3275091331) | sarg.alphaimplants | native.health; native.surgery | Yes |
| [Alpha Mechs](https://steamcommunity.com/sharedfiles/filedetails/?id=2973169158) | sarg.alphamechs | native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Alpha Memes](https://steamcommunity.com/sharedfiles/filedetails/?id=2661356814) | Sarg.AlphaMemes | native.health; native.memories (memory thoughts only); native.pawns; native.surgery; native.traits | Yes |
| [Alpha Skills](https://steamcommunity.com/sharedfiles/filedetails/?id=3448953006) | sarg.alphaskills | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Ancient amorphous threat](https://steamcommunity.com/sharedfiles/filedetails/?id=3313313985) | XMB.AncientThreat.MO | native.backstories; native.health; native.mechs; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Ancient Genes](https://steamcommunity.com/sharedfiles/filedetails/?id=3284073514) | bartalmey.ancientgenes | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Ancient urban ruins](https://steamcommunity.com/sharedfiles/filedetails/?id=3316062206) | XMB.AncientUrbanrUins.MO | native.backstories; native.health; native.memories (memory thoughts only); native.quests; native.surgery; native.traits | Yes |
| [Anomalies Expected](https://steamcommunity.com/sharedfiles/filedetails/?id=3240752689) | MrHydralisk.AnomaliesExpected | native.conditions; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Anomaly Ability Genes 异象能力基因](https://steamcommunity.com/sharedfiles/filedetails/?id=3719255171) | yuzxez.anomalyabilitygenes | native.genes | Yes |
| [Anomaly Research Asteroid](https://steamcommunity.com/sharedfiles/filedetails/?id=3527726648) | zoarak.anomalyplat | native.conditions | Yes |
| [Anthrosonae](https://steamcommunity.com/sharedfiles/filedetails/?id=2902258418) | ATK.Anthrosonae | native.genes | Yes |
| [Apex Mechanoids](https://steamcommunity.com/sharedfiles/filedetails/?id=3792325789) | Mortis.ApexMechanoids | native.abilities; native.conditions; native.health; native.mechs; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Archotech Genetics](https://steamcommunity.com/sharedfiles/filedetails/?id=2995858859) | Neronix17.Archotech.Genetics | native.genes; native.health; native.surgery | Yes |
| [Ariandel Library](https://steamcommunity.com/sharedfiles/filedetails/?id=3665997350) | Ariandel.AriandelLibrary | native.abilities; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Ascension Megacorp](https://steamcommunity.com/sharedfiles/filedetails/?id=3678583415) | AOBA.USAC.Core | native.backstories; native.health; native.mechs; native.pawns; native.surgery | Yes |
| [Automated Prison Security 自动监狱安防](https://steamcommunity.com/sharedfiles/filedetails/?id=3809896441) | isle.prisonsecurity | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Ayameduki Tweaks 2.0](https://steamcommunity.com/sharedfiles/filedetails/?id=3429757626) | WRK.AyaTweaks2 | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Beliar Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3237072670) | ElSov.Beliar | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Big and Small - Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=2925432336) | RedMattis.BetterPrerequisites | native.backstories; native.genes; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits | Yes |
| [Big and Small - Genes & More](https://steamcommunity.com/sharedfiles/filedetails/?id=2920751126) | RedMattis.BigSmall.Core | native.abilities; native.genes; native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Big and Small - Lamias and other Snake-People](https://steamcommunity.com/sharedfiles/filedetails/?id=2908225858) | RedMattis.LamiasAndOtherSnakes | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Big and Small - Simply Robots](https://steamcommunity.com/sharedfiles/filedetails/?id=3360323573) | RedMattis.BigSmall.SimpleAndroids | native.backstories; native.health; native.surgery | Yes |
| [Big and Small - Slimes](https://steamcommunity.com/sharedfiles/filedetails/?id=3027202907) | RedMattis.BSSlimes | native.genes; native.health; native.surgery | Yes |
| [Big and Small - Vampires and the Undead](https://steamcommunity.com/sharedfiles/filedetails/?id=2926556467) | RedMattis.Undead | native.genes; native.health; native.incidents; native.memories (memory thoughts only); native.surgery | Yes |
| [Bionic Pawns](https://steamcommunity.com/sharedfiles/filedetails/?id=3755254744) | Xercaine.Spacer.Bionics | native.health; native.surgery | Yes |
| [Biotech for Gravship](https://steamcommunity.com/sharedfiles/filedetails/?id=3722358861) | als.biotechgravship | native.genes; native.health; native.mechs; native.surgery | Yes |
| [Bulwarkon Mechanoid](https://steamcommunity.com/sharedfiles/filedetails/?id=3750318679) | Aliza.BulwarkonMechanoid | native.abilities; native.health; native.mechs; native.surgery | Yes |
| [Caravan Adventures](https://steamcommunity.com/sharedfiles/filedetails/?id=2558957509) | iforgotmysocks.CaravanAdventures | native.abilities; native.conditions; native.health; native.incidents; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Celestials](https://steamcommunity.com/sharedfiles/filedetails/?id=2483263407) | neronix17.celestials | native.health; native.surgery | Yes |
| [ChezhouLib](https://steamcommunity.com/sharedfiles/filedetails/?id=3595247479) | Chezhou.ChezhouLib.lib | native.health; native.surgery | Yes |
| [Chougou Mechs](https://steamcommunity.com/sharedfiles/filedetails/?id=3531074246) | Chougou.ChougouMechs.core | native.backstories; native.health; native.mechs; native.surgery | Yes |
| [Cinder Armor For DMS Mech](https://steamcommunity.com/sharedfiles/filedetails/?id=3697648629) | CN.CinderArmorForDMS | native.health; native.surgery | Yes |
| [Cinders Eroded Weapons Extended (Forked)](https://steamcommunity.com/sharedfiles/filedetails/?id=3668621614) | Asiimoves.CindersErodedWeaponsaExtendedForked | native.backstories; native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Cinders of the Embergarden](https://steamcommunity.com/sharedfiles/filedetails/?id=3421959154) | BreadMo.Cinders | native.backstories; native.genes; native.health; native.mechs; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits | Yes |
| [Combat Extended](https://steamcommunity.com/sharedfiles/filedetails/?id=2890901044) | CETeam.CombatExtended | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits | Yes |
| [Complementary Odyssey](https://steamcommunity.com/sharedfiles/filedetails/?id=3546612303) | MrHydralisk.ComplementaryOdyssey | native.health; native.surgery | Yes |
| [Compressed Raid Continued](https://steamcommunity.com/sharedfiles/filedetails/?id=3475786927) | vk.compressedraid.latestContinued | native.health; native.surgery | Yes |
| [Configurable Shield & Regeneration Genes](https://steamcommunity.com/sharedfiles/filedetails/?id=3765820519) | Aliza.ConfigurableShieldRegenerationGenes | native.genes; native.health; native.surgery | Yes |
| [Custom Quest Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=2978572782) | HaiLuan.CustomQuestFramework | native.health; native.surgery | Yes |
| [Cyanobot's Genes](https://steamcommunity.com/sharedfiles/filedetails/?id=2889844371) | cyanobot.CyanobotsGenes | native.genes; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits | Yes |
| [Cybranian - Anomaly Lich](https://steamcommunity.com/sharedfiles/filedetails/?id=3721499873) | DimonSever000.AnomalyLich | native.health; native.mental_states; native.surgery | Yes |
| [Cybranian - Events](https://steamcommunity.com/sharedfiles/filedetails/?id=2599784515) | DimonSever000.Events.Specific | native.conditions; native.memories (memory thoughts only); native.mental_states | Yes |
| [Dark Psychic Rituals: The Following](https://steamcommunity.com/sharedfiles/filedetails/?id=3596468709) | DPRTF.DarkPsychicRituals.Sentinel | native.health; native.surgery | Yes |
| [Dellin's Milira Attire Expaned](https://steamcommunity.com/sharedfiles/filedetails/?id=3648344084) | Dellin.Ariandel.MiliraAttireExpaned | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits | Yes |
| [Det's Xenotypes - Avaloi](https://steamcommunity.com/sharedfiles/filedetails/?id=3199692643) | det.avaloi | native.genes; native.health; native.surgery | Yes |
| [Det's Xenotypes - Boglegs](https://steamcommunity.com/sharedfiles/filedetails/?id=3146564944) | det.boglegs | native.genes; native.health; native.surgery | Yes |
| [Det's Xenotypes - Keshig](https://steamcommunity.com/sharedfiles/filedetails/?id=3376864722) | det.keshig | native.health; native.surgery | Yes |
| [Det's Xenotypes - Stoneborn](https://steamcommunity.com/sharedfiles/filedetails/?id=2888722722) | det.stoneborn | native.genes | Yes |
| [Dlc collaboration - Void universe](https://steamcommunity.com/sharedfiles/filedetails/?id=3587277884) | HaiLuan.VoidUniverse | native.conditions; native.health; native.memories (memory thoughts only); native.pawns; native.surgery | Yes |
| [Dubs Rimatomics](https://steamcommunity.com/sharedfiles/filedetails/?id=1127530465) | Dubwise.Rimatomics | native.conditions; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Dungeon Pack (Continued)](https://steamcommunity.com/sharedfiles/filedetails/?id=3765496911) | Mlie.DungeonPack | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [EBSG Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=3112549163) | EBSG.Framework | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Elite Raid Plus](https://steamcommunity.com/sharedfiles/filedetails/?id=3598139446) | trigger.eliteraidplus | native.health; native.surgery | Yes |
| [EPOE-Forked: Royalty DLC expansion](https://steamcommunity.com/sharedfiles/filedetails/?id=2008970276) | vat.epoeforkedroyalty | native.health; native.surgery | Yes |
| [Evolutionary Gene](https://steamcommunity.com/sharedfiles/filedetails/?id=3615049891) | custom.evolutionarygene | native.genes; native.health; native.surgery | Yes |
| [EvolvedOrgansRedux](https://steamcommunity.com/sharedfiles/filedetails/?id=3648390934) | StatistNo1.EvolvedOrgansRedux | native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Expanded Archite Genes](https://steamcommunity.com/sharedfiles/filedetails/?id=3039936922) | EBSG.Archite | native.genes; native.health (workflow guards apply); native.memories (memory thoughts only) | Yes |
| [Expanded Prosthetics and Organ Engineering - Forked](https://steamcommunity.com/sharedfiles/filedetails/?id=1949064302) | vat.epoeforked | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Extra Armor For Cinder](https://steamcommunity.com/sharedfiles/filedetails/?id=3572694485) | CN.AmorphousArmor | native.health; native.surgery | Yes |
| [Fallout Chems](https://steamcommunity.com/sharedfiles/filedetails/?id=3609770455) | Seti.FalloutChems | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Fantasy Biotech](https://steamcommunity.com/sharedfiles/filedetails/?id=3632363090) | vr.fantasy.biotech | native.health; native.incidents; native.mechs; native.quests; native.surgery | Yes |
| [Flat Earth](https://steamcommunity.com/sharedfiles/filedetails/?id=3778836858) | alino.flatearth | native.conditions; native.memories (memory thoughts only); native.quests | Yes |
| [Flesh Hive](https://steamcommunity.com/sharedfiles/filedetails/?id=3800058953) | HaiLuan.FleshHive | native.abilities; native.health; native.memories (memory thoughts only); native.pawns; native.surgery | Yes |
| [Fleshbeast Colony](https://steamcommunity.com/sharedfiles/filedetails/?id=3707199661) | FB.DemobRoka | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Fortifications - Industrial](https://steamcommunity.com/sharedfiles/filedetails/?id=2561619583) | Aoba.Fortress.Industrial | native.health; native.surgery | Yes |
| [Fortified Features Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=3498575851) | AOBA.Framework | native.health; native.mental_states; native.surgery | Yes |
| [Gene Evolution](https://steamcommunity.com/sharedfiles/filedetails/?id=3781552257) | MetaVendetta23.GeneEvolution | native.genes | Yes |
| [Gene-Ripper Gene](https://steamcommunity.com/sharedfiles/filedetails/?id=3596481890) | jaeger972.generippergene | native.genes | Yes |
| [Gloomy Dragonian race](https://steamcommunity.com/sharedfiles/filedetails/?id=2960593459) | RooAndGloomy.DragonianRaceMod | native.abilities; native.traits | Yes |
| [Gloria A Las Plagas](https://steamcommunity.com/sharedfiles/filedetails/?id=3708476081) | CCDevelopment.GloriaLasPlagas.0003 | native.genes; native.health; native.surgery | Yes |
| [Gojis-Misc-Genes](https://steamcommunity.com/sharedfiles/filedetails/?id=3775114861) | Goji.GojisMiscGenes | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [GravTech](https://steamcommunity.com/sharedfiles/filedetails/?id=3545374124) | als.gravtech | native.health; native.surgery | Yes |
| [GravTide](https://steamcommunity.com/sharedfiles/filedetails/?id=3779600989) | gravtide.mod | native.conditions; native.genes; native.health; native.memories (memory thoughts only); native.quests; native.surgery | Yes |
| [Hauts' Added Traits](https://steamcommunity.com/sharedfiles/filedetails/?id=3422847391) | Hautarche.HautsTraits | native.backstories; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits; vef.abilities; vpe.psycasts | Yes |
| [Hauts' Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=3422815711) | Hautarche.HautsFramework | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Highborn Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3380776687) | ElSov.Highborn | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Hunterphage Xenotype (Continued)](https://steamcommunity.com/sharedfiles/filedetails/?id=3542200033) | Mlie.HunterphageXenotype | native.genes; native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Ideology Scavenger Role](https://steamcommunity.com/sharedfiles/filedetails/?id=3565039115) | amegakull.SCVRole | native.health; native.surgery | Yes |
| [Immortal Regenerant Gene](https://steamcommunity.com/sharedfiles/filedetails/?id=3610093707) | custom.immortalregenerant | native.health; native.surgery | Yes |
| [Industrial Tribal Medicine](https://steamcommunity.com/sharedfiles/filedetails/?id=3530852175) | Argon.IndustrialTribalMedicine | native.health; native.surgery | Yes |
| [Integrated Genes](https://steamcommunity.com/sharedfiles/filedetails/?id=2884115974) | Turnovus.Biotech.IntegratedGenes | native.genes; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [ISEKAI CREATURES ADD-ON - Isekai RPG Leveling](https://steamcommunity.com/sharedfiles/filedetails/?id=3757560461) | Poupun.IsekaiCreaturesAddon | native.conditions | Yes |
| [ISEKAI DUNGEONS ADD-ON - Isekai RPG Leveling](https://steamcommunity.com/sharedfiles/filedetails/?id=3813394602) | Poupun.IsekaiDungeonsAddon | native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [ISEKAI RPG LEVELING](https://steamcommunity.com/sharedfiles/filedetails/?id=3657580708) | JellyCreative.IsekaiLeveling | native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Kiiro Race](https://steamcommunity.com/sharedfiles/filedetails/?id=2988200143) | Ancot.KiiroRace | native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Kiiro Story: Events Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=3036480100) | Ancot.KiiroStoryEventsExpanded | native.incidents; native.memories (memory thoughts only); native.traits | Yes |
| [Kimchianz Xenotype (Beta)](https://steamcommunity.com/sharedfiles/filedetails/?id=3752498193) | Kimchibok.KimchianzBeta | native.backstories; native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Lactation Expansion](https://steamcommunity.com/sharedfiles/filedetails/?id=3489491945) | Euterpe.MilkyTitfuck | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Legion, The Undying Soldiers Race and Faction](https://steamcommunity.com/sharedfiles/filedetails/?id=3672151247) | Dogdough.Aegiscorp | native.health; native.mechs; native.memories (memory thoughts only); native.pawns; native.quests; native.surgery; native.traits | Yes |
| [lem's bionics and implants](https://steamcommunity.com/sharedfiles/filedetails/?id=3798428702) | marsflight247.lemplants | native.health; native.surgery | Yes |
| [Leo - Events](https://steamcommunity.com/sharedfiles/filedetails/?id=3730168769) | Leo.EventsExpanded | native.conditions; native.incidents; native.mechs; native.memories (memory thoughts only); native.mental_states; native.pawns | Yes |
| [Maru Race](https://steamcommunity.com/sharedfiles/filedetails/?id=2817638066) | VAMV.MaruRaceMod | native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Mechanoid Invaders](https://steamcommunity.com/sharedfiles/filedetails/?id=3418853181) | EL.InvMech | native.health; native.mechs; native.surgery | Yes |
| [Mechanoid Upgrades](https://steamcommunity.com/sharedfiles/filedetails/?id=3365118555) | GoGaTio.MechanoidUpgrades | native.abilities; native.conditions; native.health; native.surgery | Yes |
| [Mechanoid Upgrades - Anomaly](https://steamcommunity.com/sharedfiles/filedetails/?id=3375627095) | GoGaTio.MechanoidUpgrades.Anomaly | native.abilities | Yes |
| [Mechanoid Upgrades - Archotech](https://steamcommunity.com/sharedfiles/filedetails/?id=3396928183) | GoGaTio.MechanoidUpgrades.Archotech | native.abilities; native.health; native.surgery | Yes |
| [Medicines+](https://steamcommunity.com/sharedfiles/filedetails/?id=2878860499) | Papito.MedicinesPlus.AT | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Miho, the celestial fox](https://steamcommunity.com/sharedfiles/filedetails/?id=2816826107) | miho.fortifiedoutremer | native.abilities; native.health; native.incidents; native.mechs; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Milian Mechanoids Upgrade](https://steamcommunity.com/sharedfiles/filedetails/?id=3586958104) | FP.MilianMechanoidsUpgrade | native.abilities | Yes |
| [Milian Prototype: Regent Queen 代行者](https://steamcommunity.com/sharedfiles/filedetails/?id=3805398647) | eddie.milianchancellor | native.abilities; native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Milira Addon: Fianchetto Variation](https://steamcommunity.com/sharedfiles/filedetails/?id=3621922658) | rabiosus.funnelmilian | native.mechs | Yes |
| [Milira Expanded - Future Technology](https://steamcommunity.com/sharedfiles/filedetails/?id=3560043283) | XiyueYM.MiliraExpandedXY | native.abilities; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Milira Expansion: YaoYao](https://steamcommunity.com/sharedfiles/filedetails/?id=3710608096) | ZuoYao.MiliraYaoYao | native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Milira Faction: Milira Imperium](https://steamcommunity.com/sharedfiles/filedetails/?id=3588393755) | Ariandel.MiliraImperium | native.abilities; native.conditions; native.health; native.mechs; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Milira Gene Expand:Flugel](https://steamcommunity.com/sharedfiles/filedetails/?id=3813345741) | SY.flugelgene | native.abilities; native.genes; native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Milira Halo activator](https://steamcommunity.com/sharedfiles/filedetails/?id=3465427330) | Milira.Haloactivator | native.health; native.surgery | Yes |
| [Milira Race](https://steamcommunity.com/sharedfiles/filedetails/?id=3256974620) | Ancot.MiliraRace | native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Milira Tech: Milian Modification](https://steamcommunity.com/sharedfiles/filedetails/?id=3496629836) | Ancot.MilianModification | native.health; native.mechs; native.surgery | Yes |
| [Milira:Wings of Democracy](https://steamcommunity.com/sharedfiles/filedetails/?id=3626456960) | sleepycot.wingsofdemocracy | native.conditions; native.health; native.mechs; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Misstall's Foods and Social Drugs](https://steamcommunity.com/sharedfiles/filedetails/?id=3481368558) | misstall.nisstallsfoodsandsocialdrugsz | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Misstall's Mec](https://steamcommunity.com/sharedfiles/filedetails/?id=3695149452) | misstall.nisstallMekanoido | native.mechs | Yes |
| [Misstall's Medicine And Combat Drugs](https://steamcommunity.com/sharedfiles/filedetails/?id=3754840387) | misstall.nisstallsmedicineandcombatdrugsz | native.health; native.surgery | Yes |
| [Misstall's Vigil Race](https://steamcommunity.com/sharedfiles/filedetails/?id=3473101740) | misstalls.VigilRacemodMain | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [MoeLotl Race](https://steamcommunity.com/sharedfiles/filedetails/?id=3292351432) | HenTaiLoliTeam.Axolotl | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Monolyn Race](https://steamcommunity.com/sharedfiles/filedetails/?id=3742031864) | ASEL.MonolynRace | native.genes; native.health; native.mechs; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits | Yes |
| [More Biotech Style Genes - All in One](https://steamcommunity.com/sharedfiles/filedetails/?id=2985153870) | MoreBiotechStyleGenes.Mod | native.genes; native.memories (memory thoughts only) | Yes |
| [More Faction Interaction (Continued)](https://steamcommunity.com/sharedfiles/filedetails/?id=2379076640) | Mlie.MoreFactionInteraction | native.incidents | Yes |
| [More Phytokin Xenotypes](https://steamcommunity.com/sharedfiles/filedetails/?id=3813857865) | pumpkin.morephytokinxenotypes | native.genes; native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [More Useful Injection](https://steamcommunity.com/sharedfiles/filedetails/?id=3575015788) | CN.MoreUsefulInjection | native.health; native.surgery | Yes |
| [Moyo2.0-Blood stained business](https://steamcommunity.com/sharedfiles/filedetails/?id=3756132017) | Nemonian.MY2 | native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [New Anomaly Threats](https://steamcommunity.com/sharedfiles/filedetails/?id=3274840013) | GoGaTio.NewAnomalyThreats | native.health; native.memories (memory thoughts only); native.pawns; native.surgery | Yes |
| [NewRatkinPlus](https://steamcommunity.com/sharedfiles/filedetails/?id=1578693166) | Solaris.RatkinRaceMod | native.abilities; native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Obsidia Expansion](https://steamcommunity.com/sharedfiles/filedetails/?id=2519492373) | ObsidiaExpansion.Core | native.abilities; native.genes; native.health; native.mechs; native.memories (memory thoughts only); native.surgery; vehicles | Yes |
| [Obsidia Expansion - Amalgams Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3476134915) | ObsidiaExpansion.Xenos.Amalgams | native.genes; native.health; native.surgery | Yes |
| [Obsidia Expansion - Arachnas Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=2990606834) | ObsidiaExpansion.Xenos.Arachnas | native.health; native.surgery | Yes |
| [Obsidia Expansion - Drakonids Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3446506494) | ObsidiaExpansion.Xenos.Drakonids | native.genes; native.health; native.surgery | Yes |
| [Obsidia Expansion - Liches Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3318124376) | ObsidiaExpansion.Xenos.Liches | native.genes; native.health; native.surgery | Yes |
| [Obsidia Expansion - Mothoids Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=2885653060) | ObsidiaExpansion.Xenos.Mothoids | native.health; native.surgery | Yes |
| [Obsidia Expansion - Nanomorph Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3539818574) | ObsidiaExpansion.Xenos.Nanomorph | native.genes; native.health; native.surgery | Yes |
| [Obsidia Expansion - Slimes Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3331598962) | ObsidiaExpansion.Xenos.Slimes | native.genes; native.health; native.surgery | Yes |
| [Obsidia Expansion - Spiderfolk Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3476134802) | ObsidiaExpansion.Xenos.Spiderfolk | native.genes; native.health; native.surgery | Yes |
| [Obsidia Expansion - Tailborne Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3511930980) | ObsidiaExpansion.Xenos.Tailborne | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Outland - Genetics](https://steamcommunity.com/sharedfiles/filedetails/?id=2910172297) | Neronix17.Outland.Genetics | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Pathfinding Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=3070914628) | pathfinding.framework | native.health; native.surgery | No — version review required |
| [Pesky's Bulwark Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3521016438) | pesky.bulwark | native.genes | Yes |
| [Pesky's Ghastly Guns](https://steamcommunity.com/sharedfiles/filedetails/?id=3469773661) | Pesky.GhastlyGuns | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Power++](https://steamcommunity.com/sharedfiles/filedetails/?id=1771402217) | aRandomKiwi.PowerPP | native.incidents | Yes |
| [Prisoner Restraints (Reupload for 1.6)](https://steamcommunity.com/sharedfiles/filedetails/?id=3549564292) | silkcircuit.resistancerestraintsmod | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Prisoners Can Betray](https://steamcommunity.com/sharedfiles/filedetails/?id=3654068121) | LightThief.PrisonersCanBetray | native.health; native.surgery | Yes |
| [Project RimFactory Revived](https://steamcommunity.com/sharedfiles/filedetails/?id=2033979700) | spdskatr.projectrimfactory | native.mechs; native.pawns | Yes |
| [Raven Race - Embers in Shadow](https://steamcommunity.com/sharedfiles/filedetails/?id=3781005562) | ZuoYao.RavenRace | native.conditions; native.health; native.mechs; native.memories (memory thoughts only); native.surgery; native.traits; vef.outposts (Outpost subclasses only) | Yes |
| [Real Ruins](https://steamcommunity.com/sharedfiles/filedetails/?id=1552146295) | Woolstrand.RealRuins | native.incidents; native.memories (memory thoughts only) | Yes |
| [ReGrowth 2](https://steamcommunity.com/sharedfiles/filedetails/?id=2260097569) | ReGrowth.BOTR.Core | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Reinforced Mechanoid 2 (Continued)](https://steamcommunity.com/sharedfiles/filedetails/?id=3541404610) | Mlie.ReinforcedMechanoid2 | native.health; native.mechs; native.mental_states; native.pawns; native.surgery | Yes |
| [Reinforced Mechanoid2 Extended](https://steamcommunity.com/sharedfiles/filedetails/?id=3458490409) | eclair155.reinforcedmechanoid2extended | native.health; native.surgery | Yes |
| [ReSplice: Charmweavers](https://steamcommunity.com/sharedfiles/filedetails/?id=3131996691) | ReSplice.XOTR.Charmweavers | native.genes; native.health; native.incidents; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [ReSplice: Core](https://steamcommunity.com/sharedfiles/filedetails/?id=3236303965) | ReSplice.XOTR.Core | native.genes | Yes |
| [RimContractors](https://steamcommunity.com/sharedfiles/filedetails/?id=3797500815) | lordfelix.RimContractors | native.backstories; native.mechs; native.pawns | Yes |
| [Rimesis](https://steamcommunity.com/sharedfiles/filedetails/?id=3767949538) | Font.Rimesis | native.conditions; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| RimJobWorld | rim.job.world | native.backstories; native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| RimJobWorld - Onahole Extension | rim.job.world.onahole.ext | native.health; native.pawns; native.surgery | Yes |
| [RimPacts - Diplomacy Overhaul](https://steamcommunity.com/sharedfiles/filedetails/?id=3762723122) | wowgag.RimPacts | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Rimpsyche - Disposition](https://steamcommunity.com/sharedfiles/filedetails/?id=3578212339) | Maux36.Rimpsyche.Disposition | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Rimsenal Faction Pack - Spacer](https://steamcommunity.com/sharedfiles/filedetails/?id=3086137468) | Rimsenal.Spacer | native.mechs | Yes |
| [Rimsenal: Murder diversified](https://steamcommunity.com/sharedfiles/filedetails/?id=725947920) | rimsenal.core | native.health; native.surgery | Yes |
| [RimZone Anomalies and Artifacts](https://steamcommunity.com/sharedfiles/filedetails/?id=3659328423) | fdp.rimzone.anoart | native.conditions; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Ring of Order: Annihilator Core](https://steamcommunity.com/sharedfiles/filedetails/?id=3809642867) | BSJ.AnnihilatorTech | native.health; native.mechs; native.surgery | Yes |
| RJW Genes | Vegapnk.rjw.genes | native.genes; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| RJW Race Support | ASMR.RJW.RaceSupport | native.health; native.surgery | Yes |
| [Romance On The Rim](https://steamcommunity.com/sharedfiles/filedetails/?id=2654432921) | telardo.RomanceOnTheRim | native.memories (memory thoughts only); native.quests | Yes |
| [Rustspark Crisis](https://steamcommunity.com/sharedfiles/filedetails/?id=3509858311) | HaiLuan.AllsparkCrisis | native.health; native.pawns; native.surgery | Yes |
| [RV with built-in PD](https://steamcommunity.com/sharedfiles/filedetails/?id=3342334887) | flammpfeil.rv | vehicles | Yes |
| [S.T.A.L.K.E.R. - Combat Medicine](https://steamcommunity.com/sharedfiles/filedetails/?id=2889226908) | Harkon.STALKER.CombatMedicine | native.health; native.memories (memory thoughts only); native.surgery | No — version review required |
| [SCV Ready](https://steamcommunity.com/sharedfiles/filedetails/?id=3354842892) | Chaiop.SCV | native.health; native.surgery | Yes |
| [Search and Rescue](https://steamcommunity.com/sharedfiles/filedetails/?id=3796056278) | CH4AcKO3.SearchAndRescue | native.health; native.surgery | Yes |
| [Silversol's Azurtrail](https://steamcommunity.com/sharedfiles/filedetails/?id=3582470804) | silversol.Azur | native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Silversol's Magna Fortress](https://steamcommunity.com/sharedfiles/filedetails/?id=3773804692) | silversol.magna | native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Silversol's maidnoid](https://steamcommunity.com/sharedfiles/filedetails/?id=3565656194) | silversol.consti | native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Silversol's SortieLion](https://steamcommunity.com/sharedfiles/filedetails/?id=3578749821) | silversol.sortie | native.health; native.mechs; native.surgery | Yes |
| [Silversol's Steelvivian](https://steamcommunity.com/sharedfiles/filedetails/?id=3630900013) | silversol.steelvivian | native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Silversol's Stormmarina](https://steamcommunity.com/sharedfiles/filedetails/?id=3666728559) | silversol.marina | native.mechs | Yes |
| [Simple Slavery Collars](https://steamcommunity.com/sharedfiles/filedetails/?id=2557274194) | TRIBeagle.simpleslaverycollars | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Sometimes Raids Go Wrong](https://steamcommunity.com/sharedfiles/filedetails/?id=1551336515) | marvinkosh.sometimesraidsgowrong | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Soul Weaver](https://steamcommunity.com/sharedfiles/filedetails/?id=3568502092) | lf.mod.SoulWeaver | native.health; native.surgery; vef.abilities; vpe.psycasts | Yes |
| Spawn RJW Genes | akiya82.spawn.rjw.genes | native.genes | Yes |
| [Tabula Rasa](https://steamcommunity.com/sharedfiles/filedetails/?id=1660622094) | neronix17.toolbox | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Tear Off Tentacles From Cinders](https://steamcommunity.com/sharedfiles/filedetails/?id=3726476444) | Wastelandlizard.TearOffTentacles | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Technical map Vehicles](https://steamcommunity.com/sharedfiles/filedetails/?id=3438366909) | MO.TechnicalMapVehicles | vehicles | Yes |
| [TF2 Medic item](https://steamcommunity.com/sharedfiles/filedetails/?id=3775212196) | tyajelo.tf2medicitem | native.health; native.memories (memory thoughts only); native.pawns; native.surgery; native.traits | Yes |
| [The Cosmic Odyssey - Combat Stimulant](https://steamcommunity.com/sharedfiles/filedetails/?id=2645283184) | Akarthus.TheCosmicOdyssey.CombatDrug | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [The Cosmic Odyssey - Godwokens](https://steamcommunity.com/sharedfiles/filedetails/?id=3453866993) | Akarthus.TheCosmicOdyssey.Godwokens | native.genes | Yes |
| [The Dead Man's Switch](https://steamcommunity.com/sharedfiles/filedetails/?id=3121742525) | Aoba.DeadManSwitch.Core | native.backstories; native.health; native.mechs; native.memories (memory thoughts only); native.pawns; native.surgery | Yes |
| [The Dead Man's Switch - AncientCorps](https://steamcommunity.com/sharedfiles/filedetails/?id=3469398006) | Aoba.DeadManSwitch.AncientCorps | native.backstories; native.health; native.mechs; native.pawns; native.surgery | Yes |
| [The Dead Man's Switch - GuardianCorps 失能机关扩展-戍卫兵团](https://steamcommunity.com/sharedfiles/filedetails/?id=3811867897) | onese.dmsxianfeng | native.mechs; native.pawns | Yes |
| [The Dead Man's Switch - Legion](https://steamcommunity.com/sharedfiles/filedetails/?id=3666117000) | Srxf.DeadManSwitch.Legion | native.health; native.mechs; native.pawns; native.surgery; vehicles | Yes |
| [The Dead Man's Switch - More Synthetics](https://steamcommunity.com/sharedfiles/filedetails/?id=3576816613) | Cephalopod.DeadManSwitch.MoreSynthetics | native.health; native.mechs; native.pawns; native.surgery | Yes |
| [The Dead Man's Switch - Motorized](https://steamcommunity.com/sharedfiles/filedetails/?id=3288321439) | Aoba.DeadManSwitch.Motorized | vehicles | Yes |
| [The Dead Man's Switch - Power Armor Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=3654511371) | ZIRI.DeadManSwitch.PowerArmorExpanded | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [The Dead Man's Switch - Renegade Clan](https://steamcommunity.com/sharedfiles/filedetails/?id=3659590639) | GoGaTio.DeadManSwitch.RenegadeClan | native.backstories; native.health; native.mechs; native.pawns; native.quests; native.surgery | Yes |
| [The Dead Man's Switch - Super Big Sister](https://steamcommunity.com/sharedfiles/filedetails/?id=3747887509) | CN.DMS_SuperBigSister | native.mechs; native.pawns | Yes |
| [The Dead Man's Switch - Synthetic](https://steamcommunity.com/sharedfiles/filedetails/?id=3374615241) | Aoba.DeadManSwitch.Synthetic | native.mechs; native.pawns | Yes |
| [The Dead Man's Switch-more dozer](https://steamcommunity.com/sharedfiles/filedetails/?id=3445608008) | DMS.MOREDozer | native.mechs; native.pawns | Yes |
| [Too Many Mods - Compats and Rebalances](https://steamcommunity.com/sharedfiles/filedetails/?id=3250762483) | Wara.toomanymods | native.backstories; vehicles | Yes |
| [Trauma Team Complete](https://steamcommunity.com/sharedfiles/filedetails/?id=3764955860) | EdelweissPirate.traumateamcomplete | native.health; native.surgery | Yes |
| [Trauma Team Missions](https://steamcommunity.com/sharedfiles/filedetails/?id=3757554739) | EdelweissPirate.traumateammissions | native.health; native.surgery | Yes |
| [Tribal Medicine](https://steamcommunity.com/sharedfiles/filedetails/?id=2950258180) | Argon.TribalMedicineRemake | native.health; native.surgery | Yes |
| [Unique Melee Weapons](https://steamcommunity.com/sharedfiles/filedetails/?id=3776637622) | shunter.uniquemeleeweapons | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Ushankas Glittertech Expansion](https://steamcommunity.com/sharedfiles/filedetails/?id=3522676478) | Ushanka.GlittertechExpansion | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Ushankas Hacking Expansion](https://steamcommunity.com/sharedfiles/filedetails/?id=3573344880) | Ushanka.HackingExpansion | native.health; native.surgery; vef.abilities; vpe.psycasts | Yes |
| [Ushankas Luciferium Expansion](https://steamcommunity.com/sharedfiles/filedetails/?id=3227008885) | Ushanka.LuciferiumExpansion | native.health; native.health (workflow guards apply); native.memories (memory thoughts only); native.surgery | Yes |
| [Ushankas Necroa Archovirus](https://steamcommunity.com/sharedfiles/filedetails/?id=3531035748) | Ushanka.NecroaArchovirus | native.health; native.surgery | Yes |
| [Utility Vehicle](https://steamcommunity.com/sharedfiles/filedetails/?id=3627439568) | UV.LUNARTIKA | vehicles | Yes |
| [Vanilla Anomaly Expanded - Insanity](https://steamcommunity.com/sharedfiles/filedetails/?id=3342412435) | VanillaExpanded.VAnomalyEInsanity | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits | Yes |
| [Vanilla Brewing Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=2186560858) | VanillaExpanded.VBrewE | native.health; native.health (workflow guards apply); native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Brewing Expanded - Coffees and Teas](https://steamcommunity.com/sharedfiles/filedetails/?id=2275449762) | VanillaExpanded.VBrewECandT | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Cooking Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=2134308519) | VanillaExpanded.VCookE | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Cooking Expanded - Bakery](https://steamcommunity.com/sharedfiles/filedetails/?id=3590516685) | VanillaExpanded.VCookEBakery | native.health; native.surgery | Yes |
| [Vanilla Cooking Expanded - Sushi](https://steamcommunity.com/sharedfiles/filedetails/?id=2158539170) | VanillaExpanded.VCookESushi | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Events Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=1938420742) | VanillaExpanded.VEE | native.conditions; native.health; native.incidents; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Expanded Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=2023507013) | OskarPotocki.VanillaFactionsExpanded.Core | native.health; native.mental_states; native.surgery | Yes |
| [Vanilla Factions Expanded - Classical](https://steamcommunity.com/sharedfiles/filedetails/?id=2787850474) | OskarPotocki.VFE.Classical | native.health; native.memories (memory thoughts only); native.surgery; vef.outposts (Outpost subclasses only) | Yes |
| [Vanilla Factions Expanded - Deserters](https://steamcommunity.com/sharedfiles/filedetails/?id=3025493377) | OskarPotocki.VFE.Deserters | native.conditions; native.health; native.incidents; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Factions Expanded - Empire](https://steamcommunity.com/sharedfiles/filedetails/?id=2938820380) | OskarPotocki.VFE.Empire | native.health; native.incidents; native.memories (memory thoughts only); native.quests; native.surgery | Yes |
| [Vanilla Factions Expanded - Insectoids 2](https://steamcommunity.com/sharedfiles/filedetails/?id=3309003431) | OskarPotocki.VFE.Insectoid2 | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Factions Expanded - Medieval 2](https://steamcommunity.com/sharedfiles/filedetails/?id=3444347874) | OskarPotocki.VFE.Medieval2 | native.backstories; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Factions Expanded - Pirates](https://steamcommunity.com/sharedfiles/filedetails/?id=2723801948) | OskarPotocki.VFE.Pirates | native.health; native.mechs; native.memories (memory thoughts only); native.surgery; native.traits; vef.abilities; vpe.psycasts | Yes |
| [Vanilla Furniture Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=1718190143) | VanillaExpanded.VFECore | native.health; native.surgery | Yes |
| [Vanilla Furniture Expanded - Security](https://steamcommunity.com/sharedfiles/filedetails/?id=1845154007) | VanillaExpanded.VFESecurity | native.health; native.surgery | Yes |
| [Vanilla Genetics Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=2801160906) | VanillaExpanded.VGeneticsE | native.health; native.memories (memory thoughts only); native.surgery; vef.abilities; vpe.psycasts | Yes |
| [Vanilla Gravship Expanded - Chapter 1](https://steamcommunity.com/sharedfiles/filedetails/?id=3609835606) | vanillaexpanded.gravship | native.conditions; native.mechs; native.memories (memory thoughts only) | Yes |
| [Vanilla Gravship Expanded - Chapter 2](https://steamcommunity.com/sharedfiles/filedetails/?id=3799737423) | vanillaexpanded.gravship2 | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Vanilla Ideology Expanded - Memes and Structures](https://steamcommunity.com/sharedfiles/filedetails/?id=2636329500) | VanillaExpanded.VMemesE | native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Vanilla Landmarks Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=3656316229) | VanillaExpanded.VExplorationE | native.conditions; native.health; native.incidents; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Psycasts Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=2842502659) | VanillaExpanded.VPsycastsE | native.conditions; native.health; native.memories (memory thoughts only); native.mental_states; native.pawns; native.surgery; native.traits; vef.abilities; vpe.psycasts | Yes |
| [Vanilla Psycasts Expanded - Hemosage](https://steamcommunity.com/sharedfiles/filedetails/?id=2990596478) | VanillaExpanded.VPE.Hemosage | native.health; native.memories (memory thoughts only); native.surgery; vef.abilities; vpe.psycasts | Yes |
| [Vanilla Psycasts Expanded - Puppeteer](https://steamcommunity.com/sharedfiles/filedetails/?id=3033779606) | VanillaExpanded.VPE.Puppeteer | native.health; native.surgery; vef.abilities; vpe.psycasts | Yes |
| [Vanilla Quests Expanded - Ancients](https://steamcommunity.com/sharedfiles/filedetails/?id=3618306875) | vanillaquestsexpanded.ancients | native.backstories; native.conditions; native.genes; native.health; native.mental_states; native.surgery; native.traits | Yes |
| [Vanilla Quests Expanded - Cryptoforge](https://steamcommunity.com/sharedfiles/filedetails/?id=3461526070) | vanillaquestsexpanded.cryptoforge | native.backstories; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Quests Expanded - Deadlife](https://steamcommunity.com/sharedfiles/filedetails/?id=3497226454) | vanillaquestsexpanded.deadlife | native.backstories; native.conditions; native.health; native.surgery | Yes |
| [Vanilla Quests Expanded - Drone Factory](https://steamcommunity.com/sharedfiles/filedetails/?id=3733951755) | vanillaquestsexpanded.dronefactory | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Vanilla Quests Expanded - The Generator](https://steamcommunity.com/sharedfiles/filedetails/?id=3411401573) | vanillaquestsexpanded.generator | native.backstories; native.conditions; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Races Expanded - Android](https://steamcommunity.com/sharedfiles/filedetails/?id=2975771801) | vanillaracesexpanded.android | native.backstories; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Vanilla Races Expanded - Archon](https://steamcommunity.com/sharedfiles/filedetails/?id=3067715093) | vanillaracesexpanded.archon | native.backstories; native.conditions; native.health; native.surgery; vef.abilities; vpe.psycasts | Yes |
| [Vanilla Races Expanded - Fungoid](https://steamcommunity.com/sharedfiles/filedetails/?id=3042690053) | vanillaracesexpanded.fungoid | native.genes; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Vanilla Races Expanded - Genie](https://steamcommunity.com/sharedfiles/filedetails/?id=2901424072) | vanillaracesexpanded.genie | native.health; native.surgery | Yes |
| [Vanilla Races Expanded - Hussar](https://steamcommunity.com/sharedfiles/filedetails/?id=2893586390) | vanillaracesexpanded.hussar | native.genes; native.health; native.surgery | Yes |
| [Vanilla Races Expanded - Insector](https://steamcommunity.com/sharedfiles/filedetails/?id=3260509684) | vanillaracesexpanded.insector | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Races Expanded - Lycanthrope](https://steamcommunity.com/sharedfiles/filedetails/?id=3114453100) | vanillaracesexpanded.lycanthrope | native.health; native.memories (memory thoughts only); native.mental_states; native.quests; native.surgery | Yes |
| [Vanilla Races Expanded - Phytokin](https://steamcommunity.com/sharedfiles/filedetails/?id=2927323805) | vanillaracesexpanded.phytokin | native.genes; native.health; native.incidents; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Races Expanded - Pigskin](https://steamcommunity.com/sharedfiles/filedetails/?id=3164798809) | vanillaracesexpanded.pigskin | native.health; native.surgery | Yes |
| [Vanilla Races Expanded - Sanguophage](https://steamcommunity.com/sharedfiles/filedetails/?id=2963116383) | vanillaracesexpanded.sanguophage | native.conditions; native.genes; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [Vanilla Races Expanded - Starjack](https://steamcommunity.com/sharedfiles/filedetails/?id=3531912428) | vanillaracesexpanded.starjack | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Races Expanded - Waster](https://steamcommunity.com/sharedfiles/filedetails/?id=2983471725) | vanillaracesexpanded.waster | native.genes; native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Storytellers Expanded - Winston Waves](https://steamcommunity.com/sharedfiles/filedetails/?id=3215569151) | VanillaStorytellersExpanded.WinstonWave | native.conditions; native.health; native.surgery | Yes |
| [Vanilla Traits Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=2296404655) | VanillaExpanded.VanillaTraitsExpanded | native.health; native.memories (memory thoughts only); native.mental_states; native.surgery; native.traits | Yes |
| [Vanilla Vehicles Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=3014906877) | OskarPotocki.VanillaVehiclesExpanded | native.memories (memory thoughts only); vehicles | Yes |
| [Vanilla Vehicles Expanded - Tier 3](https://steamcommunity.com/sharedfiles/filedetails/?id=3047892432) | OskarPotocki.VanillaVehiclesExpandedTier3 | native.memories (memory thoughts only); vehicles | Yes |
| [Vanilla Weapons Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=1814383360) | VanillaExpanded.VWE | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Vanilla Weapons Expanded - Non-Lethal](https://steamcommunity.com/sharedfiles/filedetails/?id=2454918354) | VanillaExpanded.VWENL | native.health; native.surgery | Yes |
| [Vehicle Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=3014915404) | SmashPhil.VehicleFramework | native.incidents; vehicles | Yes |
| [Vehicle Map Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=3426502333) | OELS.VehicleMapFramework | vehicles | Yes |
| [Vigil Race Genes and Xenotypes](https://steamcommunity.com/sharedfiles/filedetails/?id=3613194563) | misstall.nisstallvigilxenotype | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [Viltrumite Xenotype](https://steamcommunity.com/sharedfiles/filedetails/?id=3329507126) | Nalzurin.ViltrumiteXenotype | native.abilities; native.backstories; native.genes; native.health; native.surgery | Yes |
| [Virtue Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=2751960076) | 22.Virtue.Frame | native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [Vivi Race](https://steamcommunity.com/sharedfiles/filedetails/?id=3241577976) | gguake.race.vivi | native.backstories; native.genes; native.health; native.memories (memory thoughts only); native.mental_states; native.surgery | Yes |
| [VPE - Ragnarok Online](https://steamcommunity.com/sharedfiles/filedetails/?id=3585317973) | VPE.Ragnarok.Sentinel | native.health; native.surgery; vef.abilities; vpe.psycasts | Yes |
| [VPE - Veincaster](https://steamcommunity.com/sharedfiles/filedetails/?id=3677511885) | VPE.Veincaster.Sentinel | native.conditions; native.health; native.memories (memory thoughts only); native.surgery; vef.abilities; vpe.psycasts | Yes |
| [Wolfein Black Science Expand](https://steamcommunity.com/sharedfiles/filedetails/?id=3717539116) | WolfeinExpand.BlackScience | native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Wolfein Race](https://steamcommunity.com/sharedfiles/filedetails/?id=3473140562) | MelonDove.WolfeinRace | native.health; native.mechs; native.memories (memory thoughts only); native.quests; native.surgery; native.traits | Yes |
| [Wolfein Race Gene Patch](https://steamcommunity.com/sharedfiles/filedetails/?id=3473178683) | Ancot.WolfeinRaceGenePatch | native.genes; native.health; native.surgery | Yes |
| [Wolfein Race GFI Expand](https://steamcommunity.com/sharedfiles/filedetails/?id=3699095346) | JL.WolfeinGFIExpanded | native.health; native.mechs; native.memories (memory thoughts only); native.pawns; native.surgery | Yes |
| [Worksites Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=3687071198) | godsfathermixtape.worksitesexpanded | native.health; native.memories (memory thoughts only); native.surgery | Yes |
| [WVC - Xenotypes and Genes](https://steamcommunity.com/sharedfiles/filedetails/?id=2886992038) | wvc.sergkart.races.biotech | native.backstories; native.genes; native.health; native.mechs; native.memories (memory thoughts only); native.surgery | Yes |
| [Yuran race](https://steamcommunity.com/sharedfiles/filedetails/?id=2844129100) | RooAndGloomy.YuranRaceMod | native.health; native.memories (memory thoughts only); native.surgery; native.traits | Yes |
| [灵能扩展:机械师战术协议 (Mechanitor Tactical Protocol)](https://steamcommunity.com/sharedfiles/filedetails/?id=3786067817) | trigger.vpe.mechanitor | native.health; native.surgery; vef.abilities; vpe.psycasts | Yes |

## Additional packages with newly available category routes

These **74 packages** had candidate or other findings and now expose traits, mental states, memories, backstories or eligible outposts through a shared provider. Other custom systems within the same package may still need individual review. A custom world-object finding does not imply that every object is an outpost.

| Mod | Package ID | Provider routes | Declares RimWorld 1.6 |
| --- | --- | --- | --- |
| [[XND] Memorable Auroras (Continued)](https://steamcommunity.com/sharedfiles/filedetails/?id=2270039395) | Mlie.XNDMemorableAuroras | native.memories (memory thoughts only) | Yes |
| [Better Pyromania](https://steamcommunity.com/sharedfiles/filedetails/?id=2624048970) | Aelanna.BetterPyromania | native.memories (memory thoughts only) | Yes |
| [Big and Small - Heaven and Hell](https://steamcommunity.com/sharedfiles/filedetails/?id=3170117364) | RedMattis.Heaven | native.backstories | Yes |
| [Big and Small - Simple Size Genes](https://steamcommunity.com/sharedfiles/filedetails/?id=3328662155) | RedMattis.BigSmall.SimpleSize | native.traits | Yes |
| [Big and Small - Xeno Insectoid Hive](https://steamcommunity.com/sharedfiles/filedetails/?id=3328812977) | RedMattis.BigSmall.Bugs | native.backstories | Yes |
| [Bundle of Trinkets](https://steamcommunity.com/sharedfiles/filedetails/?id=3592047709) | Seti.Medieval.Trinkets | native.memories (memory thoughts only) | Yes |
| [Can't You See I'm Busy](https://steamcommunity.com/sharedfiles/filedetails/?id=3688506794) | sum117.cantyouseeimbusy | native.memories (memory thoughts only) | Yes |
| [Conservationist and Polluter - Mort's Ideologies: Memes and Precepts](https://steamcommunity.com/sharedfiles/filedetails/?id=2936965087) | MortStrudel.MortIdeologyEnv | native.memories (memory thoughts only) | Yes |
| [Death Note](https://steamcommunity.com/sharedfiles/filedetails/?id=3660590162) | xa1ren.deathnote | native.memories (memory thoughts only) | Yes |
| [Det's Xenotypes - Brawnum](https://steamcommunity.com/sharedfiles/filedetails/?id=3429572581) | det.brawnum | native.memories (memory thoughts only) | Yes |
| [Det's Xenotypes - Buzzers](https://steamcommunity.com/sharedfiles/filedetails/?id=3545293786) | det.buzzers | native.memories (memory thoughts only) | Yes |
| [Det's Xenotypes - Venators](https://steamcommunity.com/sharedfiles/filedetails/?id=3140248688) | det.venators | native.memories (memory thoughts only) | Yes |
| [Empiricism and Faith - Mort's Ideologies: Memes and Precepts](https://steamcommunity.com/sharedfiles/filedetails/?id=2948947009) | MortStrudel.MortIdeologySciFai | native.memories (memory thoughts only) | Yes |
| [Exosuit Framework](https://steamcommunity.com/sharedfiles/filedetails/?id=3352894993) | Aoba.Exosuit.Framework | native.memories (memory thoughts only) | Yes |
| [Faction Territories and Vassalage](https://steamcommunity.com/sharedfiles/filedetails/?id=3626725895) | jaeger972.factionterritories | vef.outposts (Outpost subclasses only) | Yes |
| [Family Ties](https://steamcommunity.com/sharedfiles/filedetails/?id=3574741456) | dickus.familyties | native.memories (memory thoughts only); native.traits | Yes |
| [Free Will](https://steamcommunity.com/sharedfiles/filedetails/?id=2580837227) | freemapa.freewill | native.memories (memory thoughts only) | Yes |
| [Free-ish Will](https://steamcommunity.com/sharedfiles/filedetails/?id=3635076848) | stoneman.freewill | native.memories (memory thoughts only) | Yes |
| [Fulfillment](https://steamcommunity.com/sharedfiles/filedetails/?id=3303447720) | Lewkah0.Fulfillment | native.memories (memory thoughts only) | No — version review required |
| [Gerrymon's Hotspring Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=3717051546) | GM.Hotspring | native.memories (memory thoughts only) | Yes |
| [GratefulRefugees](https://steamcommunity.com/sharedfiles/filedetails/?id=3662641911) | cocoapebbles.gratefulrefugees | native.memories (memory thoughts only) | Yes |
| HaremMasterTrait | Onslort.RJW.HaremMasterTrait | native.traits | Yes |
| [Harvest Organs Post Mortem Continued](https://steamcommunity.com/sharedfiles/filedetails/?id=1204502413) | Smuffle.HarvestOrgansPostMortem | native.memories (memory thoughts only) | Yes |
| [Home Sweet Home](https://steamcommunity.com/sharedfiles/filedetails/?id=3300829226) | Lewkah0.HomeSweetHome | native.memories (memory thoughts only) | Yes |
| [Humanoid Alien Races](https://steamcommunity.com/sharedfiles/filedetails/?id=839005762) | erdelf.HumanoidAlienRaces | native.memories (memory thoughts only); native.traits | Yes |
| [I Miss My Limbs](https://steamcommunity.com/sharedfiles/filedetails/?id=3301381349) | lewkah0.imissmylimb | native.memories (memory thoughts only); native.traits | Yes |
| [I will be back](https://steamcommunity.com/sharedfiles/filedetails/?id=3400259397) | HaiLuan.IWBB | native.memories (memory thoughts only) | Yes |
| [I'm Proud of it!](https://steamcommunity.com/sharedfiles/filedetails/?id=3305125244) | zrex.ImProudOfIt | native.memories (memory thoughts only) | Yes |
| [Implants Ripper](https://steamcommunity.com/sharedfiles/filedetails/?id=3801329151) | MaoSaur.ImplantsRipper | native.memories (memory thoughts only) | Yes |
| [Infusion 2 - Continued](https://steamcommunity.com/sharedfiles/filedetails/?id=3536247747) | sk.infusion | native.memories (memory thoughts only) | Yes |
| [Intimacy - Friends n' Lovers](https://steamcommunity.com/sharedfiles/filedetails/?id=3498422643) | LovelyDovey.Sex.WithEuterpe | native.memories (memory thoughts only); native.traits | Yes |
| [Intimacy - Homely](https://steamcommunity.com/sharedfiles/filedetails/?id=3767605238) | Homely.PillowTalk.WithEuterpe | native.memories (memory thoughts only) | Yes |
| [Job Satisfaction](https://steamcommunity.com/sharedfiles/filedetails/?id=3299411463) | Lewkah0.JobSatisfaction | native.memories (memory thoughts only) | Yes |
| [Kiiro Catmint Food Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=3669848797) | PikaJo.KiiroCatmintFoodExpend | native.memories (memory thoughts only) | Yes |
| [Mature Ladies](https://steamcommunity.com/sharedfiles/filedetails/?id=3482445541) | SexWith.HotHags.Uuuuu | native.traits | Yes |
| [Menagerist - Mort's Ideologies: Memes and Precepts](https://steamcommunity.com/sharedfiles/filedetails/?id=3514363516) | MortStrudel.MortIdeologySpecWork | native.memories (memory thoughts only) | Yes |
| [Milira Event Story Expand: The Tale of Milira](https://steamcommunity.com/sharedfiles/filedetails/?id=3477405110) | Pakerwot.MiliraEventandStortExpandTheTaleofMilira | native.memories (memory thoughts only); vef.outposts (Outpost subclasses only) | Yes |
| [Milton's Balanced Backgrounds](https://steamcommunity.com/sharedfiles/filedetails/?id=3763599107) | milton.balancedbackgrounds | native.backstories | Yes |
| [Noble and Psycaster Furniture](https://steamcommunity.com/sharedfiles/filedetails/?id=3800595973) | morton.NobleandPsycasterFurniture | native.memories (memory thoughts only) | Yes |
| [Obsidia Expansion - Paths and Stories](https://steamcommunity.com/sharedfiles/filedetails/?id=3599690177) | ObsidiaExpansion.Modular.Backstories | native.backstories; native.memories (memory thoughts only); native.traits | Yes |
| [Possessions Plus](https://steamcommunity.com/sharedfiles/filedetails/?id=3535813796) | Side1iner.PossessionsPlus | native.memories (memory thoughts only) | Yes |
| [Pray for a Miracle Ritual](https://steamcommunity.com/sharedfiles/filedetails/?id=3784262587) | Arkymn.PrayForMiracleRitual | native.memories (memory thoughts only) | Yes |
| [Prison Labor](https://steamcommunity.com/sharedfiles/filedetails/?id=1899474310) | avius.prisonlabor | native.memories (memory thoughts only) | Yes |
| [Progression: Education](https://steamcommunity.com/sharedfiles/filedetails/?id=3590863552) | ferny.ProgressionEducation | native.traits | Yes |
| [PsyStealer](https://steamcommunity.com/sharedfiles/filedetails/?id=3805150646) | MaoSaur.PsyStealer | native.memories (memory thoughts only) | Yes |
| [Questing Meme](https://steamcommunity.com/sharedfiles/filedetails/?id=2826539854) | SirMashedPotato.QuestingMeme | native.memories (memory thoughts only) | Yes |
| [Ready to Rumble!](https://steamcommunity.com/sharedfiles/filedetails/?id=3704609159) | OCEANTest8.ManualPrepMode | native.memories (memory thoughts only) | Yes |
| [Rimpsyche - Personality Core](https://steamcommunity.com/sharedfiles/filedetails/?id=3535112473) | Maux36.Rimpsyche | native.memories (memory thoughts only) | Yes |
| [RimRelations](https://steamcommunity.com/sharedfiles/filedetails/?id=3737617982) | reo.rimrelations | native.memories (memory thoughts only) | Yes |
| [RimTraits - General Traits](https://steamcommunity.com/sharedfiles/filedetails/?id=2206957172) | Sierra.RT.GeneralTraits | native.memories (memory thoughts only); native.traits | Yes |
| RJW Sexperience | rjw.sexperience | native.traits | Yes |
| RJW Sexperience Ideology | rjw.sexperience.ideology | native.memories (memory thoughts only) | Yes |
| [Seal's Backstory Collection](https://steamcommunity.com/sharedfiles/filedetails/?id=3521987415) | sneezingseal.sealsbackstorycollection | native.backstories | Yes |
| [Silent Foot](https://steamcommunity.com/sharedfiles/filedetails/?id=3582303947) | ggfirst.DelicateFeet | native.traits | Yes |
| [Simple Shock Collar](https://steamcommunity.com/sharedfiles/filedetails/?id=3355202541) | Balduin.SimpleShockCollar | native.memories (memory thoughts only) | No — version review required |
| [The Sovereignty - A Faction Expansion](https://steamcommunity.com/sharedfiles/filedetails/?id=3488373703) | Machinists.IfItWas.Epic | native.backstories; native.memories (memory thoughts only) | Yes |
| [TV is Educational](https://steamcommunity.com/sharedfiles/filedetails/?id=2921021769) | Mlie.TVIsEducational | native.memories (memory thoughts only) | Yes |
| [Vanilla Aspirations Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=3268401022) | VanillaExpanded.VanillaAspirationsExpanded | native.memories (memory thoughts only) | Yes |
| [Vanilla Backstories Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=2861806869) | VanillaExpanded.VanillaBackstoriesExpanded | native.backstories | Yes |
| [Vanilla Cooking Expanded - Haute](https://steamcommunity.com/sharedfiles/filedetails/?id=3455694362) | VanillaExpanded.VCookEHaute | native.memories (memory thoughts only) | Yes |
| [Vanilla Factions Expanded - Tribals](https://steamcommunity.com/sharedfiles/filedetails/?id=3079786283) | OskarPotocki.VFE.Tribals | native.memories (memory thoughts only) | Yes |
| [Vanilla Fishing Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=1914064942) | VanillaExpanded.VCEF | native.memories (memory thoughts only); native.traits | Yes |
| [Vanilla Food Variety Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=3334272487) | VanillaExpanded.VanillaFoodVarietyExpanded | native.memories (memory thoughts only) | Yes |
| [Vanilla Furniture Expanded - Art](https://steamcommunity.com/sharedfiles/filedetails/?id=1968134023) | VanillaExpanded.VFEArt | native.memories (memory thoughts only) | Yes |
| [Vanilla Furniture Expanded - Factory](https://steamcommunity.com/sharedfiles/filedetails/?id=3686924415) | VanillaExpanded.VFEFactory | native.memories (memory thoughts only) | Yes |
| [Vanilla Furniture Expanded - Spacer Module](https://steamcommunity.com/sharedfiles/filedetails/?id=2028381079) | VanillaExpanded.VFESpacer | native.memories (memory thoughts only) | Yes |
| [Vanilla Outposts Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=2688941031) | vanillaexpanded.outposts | vef.outposts (Outpost subclasses only) | Yes |
| [Vanilla Outposts Expanded - Factory Add-On](https://steamcommunity.com/sharedfiles/filedetails/?id=3658808349) | Scorpio.VOEFactory | vef.outposts (Outpost subclasses only) | Yes |
| [Vanilla Races Expanded - Highmate](https://steamcommunity.com/sharedfiles/filedetails/?id=2995385834) | vanillaracesexpanded.highmate | native.memories (memory thoughts only); native.traits | Yes |
| [Vanilla Races Expanded - Saurid](https://steamcommunity.com/sharedfiles/filedetails/?id=2880990495) | vanillaracesexpanded.saurid | native.memories (memory thoughts only) | Yes |
| [Vanilla Social Interactions Expanded](https://steamcommunity.com/sharedfiles/filedetails/?id=2439736083) | VanillaExpanded.VanillaSocialInteractionsExpanded | native.memories (memory thoughts only) | Yes |
| [Walk Around Park](https://steamcommunity.com/sharedfiles/filedetails/?id=3775681844) | sugarcube.walkaroundpark | native.memories (memory thoughts only) | Yes |
| [Wolfein Allegiance](https://steamcommunity.com/sharedfiles/filedetails/?id=3707497233) | leopoko.wolfeinallegiance | native.memories (memory thoughts only) | Yes |
| [瓦莱丽的米莉拉扩展包](https://steamcommunity.com/sharedfiles/filedetails/?id=3422615030) | Valeries.miliraexpansion | native.memories (memory thoughts only) | Yes |

## Reporting an adapter issue

Include framework version, provider ID, capability, selected defName, target type, active mod versions and the relevant error. Remove personal information from logs. Report whether the operation failed validation, failed during execution or failed after save/load. This helps distinguish a missing integration from an unmet native prerequisite.
