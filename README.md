# Extremely hacky implementation of Mixtral 8x7B

Mistral dropped the new MoE model this morning: https://twitter.com/MistralAI/status/1733150512395038967

This is an attempt to hack original Llama codebase to load it. The implementation is very naive and slow.

You need 2 x 80Gb or 4 x 40Gb cards to load it.

Implementation:
* remove model parallelism for simplicity
* shard experts in the specified number of GPUs
* reverse engineer MoE implementation based on https://arxiv.org/pdf/2211.15841.pdf (seems vanilla MoE)

**WARNING**: There's no official reference model code. This implementation might be wrong. At least the generation looks coherent which is a good sign :)

## Usage

Download the weights for Mixtral from HF or Torrent. HF is the easiest: https://huggingface.co/someone13574/mixtral-8x7b-32kseqlen/tree/main . Make sure you consolidate the weights

Run with 2 GPUs (~45GB required in each):

```bash
python example_text_completion.py path/to/mixtral/ path/to/mixtral/tokenizer.model
```

To run with 4 GPUs pass `--num-gpus 4`.

Edit prompt in the example if needed.

## Sample output

Mistral hallucinates about Mistral:

```
Mistral.ai is a company that
> provides a platform for building, training, and deploying AI models.

The platform offers a variety of tools and services that can help developers and data scientists build and train AI models.

Some of the key features of Mistral.ai's platform include:

- A drag-and-drop

==================================

Simply put, the theory of relativity states that
> 1) the laws of physics are the same for all observers in uniform motion relative to one another, and 2) the speed of light in a vacuum is the same for all observers, regardless of their relative motion or of the motion of the light source.

The first postulate, the principle of

==================================

A brief message congratulating the team on the launch:

        Hi everyone,

        I just
> wanted to say a big congratulations on the launch of your new website.

        I think it looks fantastic and I am sure it will be a great success.

        Well done everyone and keep up the good work.

        Best wishes,

        XXXX

==================================

Translate English to French:

        sea otter => loutre de mer
        peppermint => menthe poivrée
        plush girafe => girafe peluche
        cheese =>
> fromage
        teddy bear => ourson en peluche
        polar bear => ours polaire
        cuddly panda => panda câlin
        fluffy sheep => mouton fluffy
        furry kitten => chaton poilu
        fuzzy

==================================
```
