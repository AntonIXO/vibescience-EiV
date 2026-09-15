---
id: eiv-persist-patch-pool-mode-in-manifest
name: Persist patch_pool_mode in the adapter manifest and restore it at load
topic_tags: []
---

## Description
Persist `patch_pool_mode` into the adapter bundle manifest's sensor_config at save time, and restore it at load time. Before this, save_adapter_bundle never wrote the field and load_adapter_bundle never passed it to the CrossModalProjector constructor, so any bundle trained with a non-default pooling operator silently reconstructed as 'avg'. Defaults to 'avg' when the key is absent, preserving every pre-2026-08-28 bundle's behaviour exactly. Code: src/training/adapter_persistence.py.
