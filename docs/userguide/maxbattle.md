# MAX Battle Tracking

Track MAX Battles to receive alerts when they appear near you.

## Basic Usage

Track by level:

```
!maxbattle level5
!maxbattle level3
```

Track all MAX Battle levels:

```
!maxbattle everything
```

Track a specific pokemon:

```
!maxbattle charizard
!maxbattle snorlax
```

## Gigantamax Filter

To only receive alerts for Gigantamax variants, add `gmax`:

```
!maxbattle level5 gmax
!maxbattle charizard gmax
```

## Filtering Options

### By Distance

```
!maxbattle level5 d500
!maxbattle everything d1000
```

### By Type

```
!maxbattle fire
!maxbattle dragon
```

### By Generation

```
!maxbattle gen1
!maxbattle gen3 level5
```

### By Form

```
!maxbattle charizard form:mega
```

### By Move

```
!maxbattle everything move/flamethrower
```

You can also filter by move and type together:

```
!maxbattle everything move/hidden-power/dragon
```

## Other Options

| Option | Description |
|--------|-------------|
| `template<n>` | Use a specific alert template |
| `clean` | Auto-delete the alert when the MAX Battle expires |

## Removing Tracking

Remove tracking for a specific level or pokemon:

```
!maxbattle remove level5
!maxbattle remove charizard
```

Remove all MAX Battle tracking:

```
!maxbattle remove everything
```

## Examples

```
!maxbattle level5                      # All level 5 MAX Battles
!maxbattle everything d500             # All levels within 500m
!maxbattle charizard gmax              # Gigantamax Charizard only
!maxbattle fire level5 d1000           # Level 5 fire types within 1km
!maxbattle gen1 everything clean       # All gen 1 MAX Battles, auto-delete
```
