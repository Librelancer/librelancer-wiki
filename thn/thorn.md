# Thorn cutscene system

Files are Lua 3.1 scripts, bytecode or text

Root object contains:

|Name|Type|
|--|--|
| `duration` | `float` |
| `objects` | `array` |
| `events` | `array` |

## Objects

- Scene
- Monitor
- Camera
- Light
- Compound mesh
- Deformable mesh
- Particle system
- Sound effect
- Marker
- Motion path

## Events

- Assign camera to monitor
- Set subtitle
- Attach entity
- Connect hardpoint
- Start sound playback
- Start object animation
- Start inversed kinematics
- Start particle effect
- Play fog animation
- Play spatial animation
- Play light animation
- Play camera animation
- Play particle effect animation
- Modify floor height
- Audio effect animation
- Reverb effect animation
- Animate object with path
