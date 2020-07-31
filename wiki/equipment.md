Equipment
=========

Equipment is a bit of a maze in Freelancer. Equipment definitions have
lot of properties and often interconnected with each other.

Common
------

These are properties common to most equipment definitions.

### Lootables

Usually these are stackable projectiles such as missiles, torpedoes,
mines and countermeasures.

| Property              | Type   | Description                                                                                                                                          |
|-----------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| loot\_appearance      | string | Reference to loot crate that will be used for visual representation.                                                                                 |
| units\_per\_container | uint   | When X amount of stackable equipment is dropped the game will generate ceil(x/units\_per\_container) lootables and assign number of units each hold. |

### Mountables

Visible equipment that is attached to hardpoints and can take damage and
be blown off.

| Property              | Type    | Description                                                                                        |
|-----------------------|---------|----------------------------------------------------------------------------------------------------|
| DA\_archetype         | string  | 3D model.                                                                                          |
| LODranges             | ...uint | Customized level of detail ranges for equipment archetype.                                         |
| hit\_pts              | uint32  | Hitpoints.                                                                                         |
| HP\_child             | string  | Hardpoint in DA\_archetype used to connect to attachment hardpoint on target. Typically HpConnect. |
| explosion\_resistance | float   | How much area of effect damage is reduced (0 - none, 1 - completely ignores AoE damage)            |
| debris\_type          | string  | Debris effect that is fired upon destruction                                                       |
| separation\_explosion | string  | Explosion effect that is fired upon destruction (separation from attached part)                    |

Weapons
-------

### Armaments

Anything that shoots: guns, turrets, launchers, etc. Armaments inherits
from Mountables.

| Property              | Type   | Desciprtion                                                                                                                                                                                                       |
|-----------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| damage\_per\_fire     | int    | Damage to hull each shot takes.                                                                                                                                                                                   |
| power\_usage          | int    | How much energy each shot takes.                                                                                                                                                                                  |
| toughness             | float  | Adds 'toughness' points. The higher the rating the less likely NPCs will attack you or bail out of fight quicker.                                                                                                 |
| dry\_fire\_sound      | string | Name of sound effect when there isn't enough energy to make a shot.                                                                                                                                               |
| use\_animation        | string | Play compound mesh animation script on each shot.                                                                                                                                                                 |
| refire\_delay         | float  | Hard cooldown before another shot could be made.                                                                                                                                                                  |
| muzzle\_velocity      | float  | Initial velocity given to projectile.                                                                                                                                                                             |
| flash\_particle\_name | string | Particle effect used for muzzle flash.                                                                                                                                                                            |
| flash\_radius         | float  | Visibility range for muzzle flash particle effect.                                                                                                                                                                |
| light\_anim           | string | Light effect?                                                                                                                                                                                                     |
| projectile\_archetype | string | Name of munition this thing shoots.                                                                                                                                                                               |
| turn\_rate            | float  | Turn rate for armaments attached to revolute hardpoints. Also affects turn rate of the barrel(s).                                                                                                                 |
| gun\_elevation        | float  | Supposedly it limited gun base and barrel angles. Apparently not used in game. Instead same is done by revolute hardpoint min/max angles the gun is attached to, and by revolute barrel joint within turret .cmp. |
| gun\_azimuth          |     |                                                                                                                                                                                                                |
| dispersion\_angle     | float  | Randomizes muzzle angle from 0 to this value if present.                                                                                                                                                          |
| AI\_range             | float  | Prevents AI pilots from using this equipment after exceeding this distance from target(s).                                                                                                                        |
| auto\_turret          | bool   | Automatic turret?                                                                                                                                                                                                 |

### Munition

Projectiles that are shot by guns: bullets, missiles, mines,
countermeasures, etc.

Munition equipment (bullets, missiles, mines, whatever):

<table>
<thead>
<tr class="header">
<th>Property</th>
<th>Type</th>
<th>Appearance</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>motor</td>
<td>string</td>
<td>Munition</td>
<td>Add acceleration motor to projectile.</td>
</tr>
<tr class="even">
<td>detonation_dist</td>
<td>float</td>
<td>Munition<br />
Mine</td>
<td>Triggers explosion at a distance from target.</td>
</tr>
<tr class="odd">
<td>explosion_arch</td>
<td>string</td>
<td>Munition<br />
Mine</td>
<td>Explosion (triggered by detonation_dist). Reference to [Explosion]\nickname in equipment INIs.</td>
</tr>
<tr class="even">
<td>one_shot_sound</td>
<td>string</td>
<td>Munition<br />
Mine<br />
Countermeasure</td>
<td>Firing shot sound.</td>
</tr>
<tr class="odd">
<td>const_effect</td>
<td>string</td>
<td>Munition<br />
Mine<br />
Countermeasure</td>
<td>Effect applied to projectile.</td>
</tr>
<tr class="even">
<td>munition_hit_effect</td>
<td>string</td>
<td>Munition</td>
<td>Effect applied to hit point. Effect will attach to object it got hit.</td>
</tr>
<tr class="odd">
<td>HP_trail_parent</td>
<td>string</td>
<td>Munition<br />
Mine</td>
<td>const_effect is applied to this hardpoint of projectile if it has a mesh.</td>
</tr>
<tr class="even">
<td>lifetime</td>
<td>float</td>
<td>Munition<br />
Mine<br />
Countermeasure</td>
<td>How long projectile lives before vanishing off.</td>
</tr>
<tr class="odd">
<td>seeker</td>
<td>string</td>
<td>Munition</td>
<td>Turns projectile into a missile. DUMB is dumb missile. SEEK will attempt to home onto target.</td>
</tr>
<tr class="even">
<td>time_to_lock</td>
<td>float</td>
<td>Munition</td>
<td>Delay before homing missile will lock onto target.</td>
</tr>
<tr class="odd">
<td>seeker_range</td>
<td>float</td>
<td>Munition</td>
<td>Homing missile tracking distance.</td>
</tr>
<tr class="even">
<td>seeker_fov_deg</td>
<td>float</td>
<td>Munition</td>
<td>Homing missile field of vision limit.</td>
</tr>
<tr class="odd">
<td>seek_dist</td>
<td>float</td>
<td>Mine</td>
<td>Maximum distance within which mines will scan for a target.</td>
</tr>
<tr class="even">
<td>max_angular_velocity</td>
<td>float</td>
<td>Munition</td>
<td>Homing missile turn speed.</td>
</tr>
<tr class="odd">
<td>top_speed</td>
<td>float</td>
<td>Mine</td>
<td>Mine maximum speed.</td>
</tr>
<tr class="even">
<td>acceleration</td>
<td>float</td>
<td>Mine</td>
<td>Mine acceleration upon following target and until reaching top speed.</td>
</tr>
<tr class="odd">
<td>owner_safe_time</td>
<td>float</td>
<td>Mine</td>
<td>Mine safety time before before it'll starting homing onto owner as well.</td>
</tr>
<tr class="even">
<td>linear_drag</td>
<td>float</td>
<td>Mine</td>
<td>Velocity drag. Used by countermeasures and mines.</td>
</tr>
<tr class="odd">
<td>range</td>
<td>float</td>
<td>Countermeasure</td>
<td>Capture range. Used by countermeasures.</td>
</tr>
<tr class="even">
<td>diversion_pctg</td>
<td>float</td>
<td>Countermeasure</td>
<td>Diversion percentage. Used by countermeasures.</td>
</tr>
<tr class="odd">
<td>force_gun_ori</td>
<td>bool</td>
<td>Munition<br />
Mine<br />
Countermeasure</td>
<td>Forces projectile orientation to HpFire hardpoint of the gun.</td>
</tr>
<tr class="even">
<td>requires_ammo</td>
<td>bool</td>
<td>Munition<br />
Mine<br />
Countermeasure</td>
<td>Requires ammo to fire.</td>
</tr>
<tr class="odd">
<td>cruise_disruptor</td>
<td>bool</td>
<td>Munition<br />
Mine?<br />
Countermeaure?</td>
<td>Damage disables engine cruise mode.</td>
</tr>
<tr class="even">
<td>damage</td>
<td>int</td>
<td>Munition</td>
<td>Appears somewhere?</td>
</tr>
<tr class="odd">
<td>weapon_type</td>
<td>string</td>
<td>Munition</td>
<td>Damage type applied by projectile.</td>
</tr>
</tbody>
</table>

### Motors

Motors provide constant acceleration to projectile on top of the
existing initial velocity.

| Property | Type  | Description                          |
|----------|-------|--------------------------------------|
| lifetime | float | How long motor will work.            |
| accel    | float | Motor will provide acceleartion.     |
| delay    | float | Delay activating motor acceleration. |

### Explosions

Explosions? EXPLOSIONS! YEAAAAAAAH!!!

Munitions refer to these, so don't get them confused with \[explosion\]
in FX\\explosions.ini, although related they are different.

| Property       | Type         | Description                                                         |
|----------------|--------------|---------------------------------------------------------------------|
| effect         | string       | Reference to FX\\effects.ini\\\[Effect\]\\nickname.                 |
| lifetime       | float, float | Explosion lifetime?                                                 |
| process        | string       | Available values: 'none', 'disappear', 'shatter'.                   |
| strength       | float        | Dunno.                                                              |
| radius         | float        | Blast radius. Apparently there's no damage falloff due to distnace? |
| hull\_damage   | int          | Damage to hull.                                                     |
| energy\_damage | int          | Damage to shield or power capacity if shield is down.               |
| impulse        | float        | Force vector applied to all moveable objects caught in radius.      |
