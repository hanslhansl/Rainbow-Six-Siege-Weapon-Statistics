#  Rainbow Six Siege Weapon Statistics
Provides detailed statistics for all weapons in Tom Clancy's Rainbow Six Siege. This includes the weapon's damage at distances up to 40 meters, fire rate, damage per second, shots to down or kill, ADS time, etc. All data was collected during operation Dread Factor and Heavy Mettle and last updated after patch Y9S1.2. A spreadsheet of the important stats can be found on [Google Sheets](https://docs.google.com/spreadsheets/d/1QgbGALNZGLlvf6YyPLtywZnvgIHkstCwGl1tvCt875Q) and as a stand-alone website on [Google Drive](https://docs.google.com/spreadsheets/d/e/2PACX-1vQ1KitQsZksdVP9YPDInxK3xE2gtu1mpUxV5_PNyE8sSm-vFINdbiL8vo9RA2CRSIbIUePLVA1GCTWZ/pubhtml).
# About how the data is collected
Generally speaking I am trying to collect as much of the data myself and by hand because Ubisoft has proven to be an unreliable source for statistics. At the same time I am trying to cut corners where possible.
## Time measuring
I am measuring time intervals by recording the game, opening the recording in a video editing program and measuring the time stamps. This method is by far not as accurate as would want it to be but I don't know of any alternative. I am recording my game at 60 fps which means that, in theory, every time measurement can deviate up to ${1\over60}s\approx0.0167s=16.7ms$ from the actual value. Unfortunatelly I have come to the conclusion that in some cases my measurements deviate consideribly more than just one sixtieth of a second. I am not sure why that is and how to fix it. Higher frame rates could solve this but my PC limits me to recording at 60 fps. In conclusion this means that every time related stat (e.g. fire rate, DPS, ads time, ...) should be taken with a grain of salt.
## Damage per bullet
I am measuring the damage values in the in-game Shooting Range which limits my measurements to distances of 5 to 40 meters (thanks Ubi). I am not using the Yellow Ping feature to measure the distance to the dummy but rather the distance value displayed on the shooting range panel. This value (just like yellow ping btw.) is a rounded up integer value (e.g 7.1m is displayed as 8m). To position yourself e.g. 8 meters away from the dummy you have to stand where the displayed distance switches from 8 to 9 meters. This is exactly what I am trying to achieve: Measure the weapon damages at the exact distances and not inbetween. This would mean that I need to measure 36 distinct damage values (from 5 to 40 meters) per weapon. However:
- Experience has shown that damage in R6S never increases over distance. It only decreases or stagnates. Therefor, if the damage at distance A is equal to the damage at distance B the damage at all distances inbetween A and B is also equal to said damage. Because of this I don't have to measure at all distances. The unmeasured but deducible damage values are supplemented automatically by the Python script.
- Experience has also shown that all weapons in R6S have exactly one damage drop-off interval. Up until drop-off start they deal the base damage. From drop-off start to drop-off end they progressively lose damage over distance and from drop-off end on they do the final damage. Because of this the damage up until 5 meters (which can't be measured in the shooting range) can be assumed to equal the damage at 5 meters. To prevent errors in case the damage drop-off interval reaches into the first 5 meters I am only applying this assumption if the measured damages at 5, 6 and 7 meters are equal. If that isn't the case I leave the damages up until 5 meters blank and test them in a custom game. It should be said that even with this precautionary measure it is still possible for me to overlook a damage drop within the first 5 meters. Those values should therefor be taken with a grain of salt.
## Hit areas
There are three body hit areas in R6S: the head, the torso and the limbs. The torso has a damage multiplier of 1 and therefor receives the base damage. This is the value I am measuring. Most weapons deal infinite damage to the head. The only exception are shotguns which deal $150\\%$ of the base damage to the head. Limb damage is a bit more complicated. As of now I believe most weapons deal $75\\%$ of the base damage to limbs. However, there exists at least one exception to this rule though, Kali's CSRX 300, which deals $60\\%$ to limbs. At some point I will test all weapon's limb damage multiplier. soon (tm).
## The extended barrel
The only attachment that affects the damage per bullet is the extended barrel. It used to increase the damage by $10\\%$ at all distances but for inexplicable reasons Ubisoft changed how the extended barrel works sometime during Y8S3 without mentioning it in any patch notes. Up until the distance at which the damage starts to drop off without the extended barrel it still increases the damage by $10\\%$. From this distance on it gets funky. It seems to delay and soften the damage drop-off at the same time but I couldn't figure out how it works exactly.

Because collecting the extended barrel damage stats independently from the base stats would mean a huge additional effort and because the new extended barrel differs from the old extended barrel only slightly and only at higher distances I am still using the old extended barrel stats: Base stats increased by $10\\%$ and rounded up. $Damage=\lceil {Damage * 1.1} \rceil$.
## Bullets per shot - Pellet count
This value is displayed in the shooting range. Most weapons shoot exactly one bullet per shot. The only exception are shotguns, most of which shoot 8 bullets per shot. For shotguns this metric is also called pellet count.
## Damage per shot
The damage per shot is calculated as $DmgPerShot = Damage * Pellets$. Only for shotguns this value varies from the damage per bullet. No measuring necessary.
## Fire rate
Currently, for all fully automatic weapons I am using the fire rates listed in-game. For all other weapons I measure the fire rates myself. I am doing this by emptying a magazine of size $n$ and measuring the time $t$ (in milliseconds) between the ammo counter decreasing the first time and the ammo counter reaching 0. The fire rate in rounds per milliseconds is calculated as ${n-1 \over t}rpms$, in rounds per second as $1000{n-1 \over t}rps$ and in rounds per minute as $60000{n-1 \over t}rpm$. It is important to subract $1$ from $n$ because the first bullet is shot immediatelly after pulling the trigger and therefor doesn't contribute to the measured time. If you didn't subtract $1$ you would get different fire rates for the same gun depending on how many bullets you shoot.

As [already mentioned](#time-measuring) my time measurements can vary up to 16.7ms from the real values. The actual fire rates are therefor in the interval defined by $60000{n-1 \over t \pm 16.7}rpm$. For example a measured fire rate of 800 rpm for a weapon with 31 bullets per magazine would mean an actual fire rate of something inbetween 794 rpm and 806 rpm. Because of this innaccuracy I am usually rounding the fire rate to a reasonable integer within said interval (e.g. measured 433.47 rpm become 430 rpm).

At some point I will measure all fully automatic weapon's fire rates as well. Just to be sure. soon (tm).
## Damage per second - DPS
The damage per second is calculated as $DPS = DmgPerShot * RPS = DmgPerShot * RPM / 60$. No measuring necessary.
## Shots to down or kill - STDOK
For a target with $x$ hp the STDOK is calculated as $\lceil {x \over Damage} \rceil$. No measuring necessary.
## Time to down or kill - TTDOK
For a target with $x$ hp the TTDOK in milliseconds is calculated as ${STDOK - 1 \over rpms}$. No measuring necessary.
## Magazine capacity
Most weapons have, in addition to the bullets loaded in the magazine, one bullet loaded in the chamber. For those weapons this value is displayed as $Capacity+1$ (e.g. $30+1$, $20+1$, etc.). For all other weapons without a bullet loaded in the chamber this value is displayed as $Capacity+0$ (e.g. $100+0$, $80+0$).
## Aim down sight time - ADS time
The time in seconds it takes to aim down sight while standing still. The laser attachment increases the ads speed by $10\\%$. The reduced ads time with laser is calculated as $ads \over 1.1$.
## Reload time
soon (tm)
