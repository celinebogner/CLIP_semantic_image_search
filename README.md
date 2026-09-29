CLIP Semantic Image Search 
=================================
Created by Celine Bogner on 9/16/26 

Contents
--------
- CLIP_semantic_image_search.ipynb   : main notebook (all core + additional features)
- photos.json                        : metadata (title, tags, source) for each image, keyed by filename
- photos/                            : image collection (full resolution)
- collection_grid.png, fig_nearest_pairs.png, fig_clusters.png, and other fig_*.png files:
  saved figures used in the report
 
Environment
-----------
Developed and tested with:
  - Python 3.12
  - conda environment named "torch"
 
Install dependencies (run once, e.g. in a fresh conda/virtualenv environment):
 
    pip install ftfy regex tqdm scikit-image scikit-learn matplotlib numpy pillow torch
    pip install git+https://github.com/openai/CLIP.git
 
(The first code cell of the notebook contains this same install command, commented out
after the first run.)
 
Running the notebook
---------------------
1. Place photos/ (the image folder) and photos.json in the same directory as the notebook.
2. Open CLIP_semantic_image_search.ipynb and run all cells in order
   (Kernel > Restart Kernel and Run All is recommended to ensure a clean run).
3. The notebook will:
   - Load and sanity-check photos.json against the contents of photos/
   - Build the labeled thumbnail grid
   - Load CLIP (ViT-B/32) and preprocess one example image for inspection
   - Compute or load cached image embeddings (see below)
   - Provide a search(query, k) function for text-to-image retrieval
   - Run the additional-feature analyses (retrieval margins, nearest pairs,
     prompt templates, clustering, and prompt ensembling)
 
Computing or loading the image cache
-------------------------------------
Image embeddings are cached in clip_cache.pt, which stores the image filenames,
the normalized embeddings, and the model name together.
 
- If clip_cache.pt is present and its stored filenames and model name match the
  current photos.json/model, the notebook loads the cached embeddings directly
  and skips re-embedding the collection.
- If the cache is missing, or the filenames/model don't match (e.g. after adding,
  removing, or renaming a photo), the notebook automatically recomputes the
  embeddings and overwrites clip_cache.pt.
- To force a full recompute, simply delete clip_cache.pt before running the notebook.
 
Note: the cache checks filenames and model name only, not image content. If you
replace a photo's file while keeping the same filename, delete the cache manually
so the embedding is recomputed.
 
Running a query
----------------
Once the embeddings are loaded (either from cache or freshly computed), search the
collection from any notebook cell with:
 
    search("a photo of a window", k=5)
 
This returns the top k (filename, similarity score) pairs, best match first.
For a labeled visual grid of the results instead of a plain list, use:
 
    show_results("a photo of a window", k=5)
 
No re-embedding of the image collection occurs when running a query; only the
text query itself is encoded at search time.