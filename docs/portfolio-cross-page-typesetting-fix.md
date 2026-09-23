# Cross-page context and PDF text-size fix (independent fork experiment)

**Status:** Personal fork note and a locally validated patch. This is not an upstream-approved or merged contribution. The Zotero PDF2zh maintainers have announced a repository-wide refactor and asked contributors to pause submissions, so no pull request is being made.

## Problem

On a dense, multi-page academic PDF, translated text could become unreadably small. Translating each page in a separate engine run improved layout, but removed the document-wide context required to recognize paragraphs split across page boundaries.

## Diagnosis

BabelDOC 0.6.2 already has a cross-page translation pass that batches the last body paragraph on one page with the first body paragraph on the next. The per-page workaround bypassed that pass. Separately, `Typesetting.preprocess_document` calculated a modal scale over the entire document and assigned that scale to paragraphs whose own optimal scale was larger. A dense or unusually long paragraph could therefore shrink unrelated text.

## Patch

The patch in [`patches/BabelDOC-0.6.2/0001-preserve-per-paragraph-scales.patch`](../patches/BabelDOC-0.6.2/0001-preserve-per-paragraph-scales.patch) targets the [BabelDOC v0.6.2 source file](https://github.com/funstory-ai/BabelDOC/blob/v0.6.2/babeldoc/format/pdf/document_il/midend/typesetting.py). It keeps each paragraph's precomputed scale instead of clamping every paragraph to a document-wide mode. The Zotero server uses the normal whole-document PDF2zh call, so BabelDOC's adjacent-page paragraph pass remains available.

This fork does not vendor BabelDOC or silently patch other users' environments. The patch is provided as a reproducible, version-specific experiment; it will need review and integration in the appropriate upstream source before it can become a general release fix.

## Local validation

Validated in a Windows setup with PDF2zh-next 2.9.0 / BabelDOC 0.6.2 and DeepSeek on a 17-page two-column paper. The whole-document run produced a 34-page alternating bilingual PDF and a 17-page Chinese-only reading copy. Across the 17 translated pages, the median CJK text size was 9.96 pt; the smallest page-level median was 7.17 pt, and the previously affected page measured 8.07 pt. The test PDF, API credentials, and logs are intentionally not included in this repository.

The inspection verified page count and visual readability. It does not establish that every PDF layout or every cross-page sentence will translate perfectly.

## Portfolio wording

Accurate wording while this is still only a fork experiment:

> Built and locally validated an AI-assisted patch for a PDF translation layout issue: preserved whole-document cross-page context while preventing a dense paragraph from shrinking unrelated text. Published a reproducible, version-pinned BabelDOC patch in a personal Zotero PDF2zh fork; the change has not been submitted to the upstream project.
