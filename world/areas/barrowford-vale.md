---
type: area
id: ar-barrowford-vale
created: 2026-07-10
updated: 2026-07-10
status: active
grid:
  cols: 9
  rows: 6
  terrain: ["mmmhhffFF","mhhhpffFF","hhpvppfff","pppccpffh","wwpppsssd","wwwpssssh"]
  rivers: [{r: 0, c: 2, k: src, rot: 5}, {r: 1, c: 1, k: e180, rot: 2}, {r: 2, c: 1, k: e120, rot: 5, flip: true}, {r: 3, c: 1, k: e120, rot: 1}]
  roads: [{r: 3, c: 0, k: e180}, {r: 3, c: 1, k: e180}, {r: 3, c: 2, k: e120, rot: 3, flip: true}, {r: 2, c: 3, k: e120, rot: 5}, {r: 2, c: 4, k: e180}, {r: 2, c: 5, k: src}]
  coasts: [{r: 3, c: 0, style: beach, edges: [1, 2]}, {r: 4, c: 2, style: beach, edges: [2, 3]}, {r: 5, c: 3, style: cliff, edges: [3]}]
subsystems_touching: ["[[ss-wolf-pressure-west-hills]]", "[[ss-nen-of-the-lake]]"]
---

# Barrowford Vale

River off the western mountains, ford at Barrowford, lake in the southwest
with Nen's Rest on its cliffed shore, marshes east, the twin towns on the
plain road, Silverwood thickening northeast. Map data above is the UI-4
source of truth; the STATE builder reads it directly (AR-1).
