# NDI-compress-matlabp
NDI-compress public library

This repository holds only MATLAB p-code (`.p` files) built from the private
source repository Waltham-Data-Science/NDI-compress with NDI-matlab's
`ndi.file.pfilemirror`. NDI-matlab uses the `V2` branch.

## Branches

- `V2` is the working branch. All rebuilds land here.
- `main` is not changed by any rebuild or by the workflow below.

## How the `.p` files are rebuilt

The workflow `.github/workflows/build-pcode.yml` (on `V2`) rebuilds
`+ndi/+compress/*.p` from the NDI-compress commit named in `build-source-ref`.

1. Merge the source change into NDI-compress `V2` (by PR, reviewed there).
2. On this repository's `V2` (by PR), set `build-source-ref` to the NDI-compress
   ref or commit to build, normally the new NDI-compress `V2` head sha. One
   line, nothing else.
3. The push to `V2` that changes `build-source-ref` starts the workflow. It
   checks out NDI-compress at that ref, checks out NDI-matlab `V2` for
   `ndi.file.pfilemirror`, runs it from `source/+ndi/+compress` into
   `+ndi/+compress` here (MATLAB R2024b), and commits any changed `.p` files to
   `V2` with a message naming the NDI-compress sha it built.

Details:

- It needs the repository secret `NDI_COMPRESS_READ_TOKEN`, a token with read
  access to Waltham-Data-Science/NDI-compress. Without it the run prints
  `NOT BUILT: secret NDI_COMPRESS_READ_TOKEN missing` and succeeds; nothing is
  built or committed.
- It runs only for pushes to `V2`, refuses any other ref, and pushes only to
  `V2` (no force-push; a push that is not a fast-forward fails).
- Only `+ndi/+compress` is mirrored; the source's test packages are not
  shipped. `pfilemirror` adds and overwrites `.p` files but never deletes one,
  so a `.p` file whose source was removed must be deleted by hand.
- The bot commit never changes `build-source-ref`, and pushes made with
  `GITHUB_TOKEN` start no workflow runs, so a rebuild does not retrigger itself.
- To rebuild the same source again, change `build-source-ref` (for example
  from a branch name to the sha it points at).
