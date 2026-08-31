# HICOCO Dataset Release

HICOCO is a test-only human-object interaction (HOI) benchmark built from COCO images. Its focus is on new verbs and interactions beyond the HICO-DET ontology.

## Files

- `annotations.json`: final per-image HICOCO annotations.
- `metadata.json`: object, verb, HOI category, and split indices used to interpret the annotations.
- `image_ids.txt`: COCO image filenames used by HICOCO.


## Images

The annotations refer to COCO images by filename and COCO image id. Set your local COCO/HICOCO image directory when loading or visualizing the dataset, for example:

```text
path/to/COCO/images
```

If redistributing HICOCO, follow the COCO image license/terms. 

## Annotation Format

`annotations.json` is a list of images. Each image entry contains:

```text
file_name      COCO image filename, e.g. 000000199951.jpg
coco_image_id  integer COCO image id
width, height  image size in pixels
interactions   list of HOI annotations for this image
```

Each interaction follows the same compact numeric style used by HICO-DET annotations:

```text
interaction_id  annotation id within the image
hoi_id          0-based HOI category id
verb_id         0-based verb id
object_id       0-based COCO-contiguous object id
subject_box     human box [x1, y1, x2, y2]
object_box      object box [x1, y1, x2, y2]
```

`metadata.json` contains the lookup tables for these ids:

```text
objects         80 COCO-contiguous object category ids and names
verbs           296 verb ids and names
hoi_categories  1072 HOI category ids with their verb/object ids and names
splits          HOI and verb id lists for known/new evaluation groups
```

## Quick Load Example

```python
import json
from pathlib import Path

root = Path('path/to/HICOCO')
annotations = json.load(open(root / 'annotations.json'))
metadata = json.load(open(root / 'metadata.json'))

verb_by_id = {v['verb_id']: v['name'] for v in metadata['verbs']}
object_by_id = {o['object_id']: o['name'] for o in metadata['objects']}

image = annotations[0]
interaction = image['interactions'][0]
print(image['file_name'])
print(verb_by_id[interaction['verb_id']], object_by_id[interaction['object_id']])
```

## Notes

- The benchmark is test-only; no training split is provided.
- Bounding boxes are stored as `[x1, y1, x2, y2]`.
- Object ids are 0-based COCO-contiguous ids, matching the detector/evaluation code.
- Verb ids are 0-based; HICO-DET verbs occupy ids 0-116, and HICOCO-added verbs occupy ids 117 and above.
- Use the lists in `metadata.json` under `splits` to identify known HICO-DET HOIs, new HOIs, known-verb novel combinations, and new-verb interactions.
