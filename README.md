# tentacle_comfy_bridge

**One capability bit from GBT V8, packaged standalone.** No. 57 of 341,
`creation` domain (Creation).

<p>
<img alt="python" src="https://img.shields.io/badge/python-3.9%2B-blue">
<img alt="license" src="https://img.shields.io/badge/license-MIT-green">
<img alt="deps" src="https://img.shields.io/badge/runtime%20dependencies-0-brightgreen">
</p>

> **第 1530 根触手 · ComfyUI 桥**（主人令 2026-10-11「ComfyUI 不是生图工具，是第 1530 根触手的底座」）：把外部 ComfyUI 当**贴图工厂／概念工厂**用，并**双向绑定** —— ① 工作流 = 契约（`state/comfy_workflows/*.json`，ComfyUI 原生节点连线格式）；② 产物**先过闸才允许落盘**（闸口径与 `comfy_native.gate_image` 同源：最短边/格式/非空/可解码）；③ **溯源自证**：workflow_sha256 + seed + ckpt_hash 全部落 `store/tentacle_comfy/ledger.jsonl`（只追加）。出网硬钉照抄 `comfyui_surface`：只许本机 http + 主机白名单 + 端口 8188 + 路径前缀 + 拒重定向。**不在位就如实回 needs_dependency（绝不假装出图）**；断连判据同源（节点数 < 900 = 断连）。op=status/queue/workflows/verify

That description is copied verbatim from the registry. `tests/test_spec.py` pins it,
so documentation drift fails the build instead of shipping quietly.

## What this package actually is

It is the **contract** for one capability: its name, its parameters, the values it
accepts, and whether it can change state that cannot be restored. It is not the
implementation — that lives in the runtime this bit was extracted from.

So `run()` goes through a narrow `Backend` protocol, and the default backend is a
stub that **says so**:

```
status="validated"   backend="stub"   data={"executed": false}
```

Never `status="ok"`. A stub that reports success is worse than no stub, because the
caller stops looking for the real thing.

## Install

```bash
git clone https://github.com/gbtweilai-star/gbt-tentacle-comfy-bridge.git
cd gbt-tentacle-comfy-bridge
python -m pip install -e ".[dev]"
```

## Use

```bash
python -m gbt_tentacle_comfy_bridge info                  # capability card
python -m gbt_tentacle_comfy_bridge spec --json           # machine-readable contract
python -m gbt_tentacle_comfy_bridge check --payload '{}'
python -m gbt_tentacle_comfy_bridge run   --payload '{}'
```

Real `info` output from this machine:

```text
tentacle_comfy_bridge  (creation)  v1.0.0
----------------------------------------
**第 1530 根触手 · ComfyUI 桥**（主人令 2026-10-11「ComfyUI 不是生图工具，是第 1530 根触手的底座」）：把外部 ComfyUI 当**贴图工厂／概念工厂**用，并**双向绑定** —— ① 工作流 = 契约（`state/comfy_workflows/*.json`，ComfyUI 原生节点连线格式）；② 产物**先过闸才允许落盘**（闸口径与 `comfy_native.gate_image` 同源：最短边/格式/非空/可解码）；③ **溯源自证**：workflow_sha256 + seed + ckpt_hash 全部落 `store/tentacle_comfy/ledger.jsonl`（只追加）。出网硬钉照抄 `comfyui_surface`：只许本机 http + 主机白名单 + 端口 8188 + 路径前缀 + 拒重定向。**不在位就如实回 needs_dependency（绝不假装出图）**；断连判据同源（节点数 < 900 = 断连）。op=status/queue/workflows/verify

gated      : False
external   : False
multi-op   : True
params     : (none)
values     : store, tentacle_comfy, ledger
```

Wire a real implementation:

```python
from gbt_tentacle_comfy_bridge import run

class MyBackend:
    name = "my-backend"
    def invoke(self, cap, payload):
        return {"cap": cap, "handled": sorted(payload)}

result = run({}, backend=MyBackend())
assert result.status == "ok"
```

## Parameters

| name | required | notes |
|---|---|---|
| — | no | the registry declares no required parameters |

Registry hint (`params_hint`): (empty)

**注册表登记的取值 / 子动作（3 个）**：`store`、`tentacle_comfy`、`ledger`

Types are deliberately absent: the registry does not declare them, and an invented
type is worse than no type, because callers believe it.

## Boundaries

- Not gated: the registry marks this bit `safe: true`.
- No external service dependency declared.
- Multi-op (`multi_op: true`): several sub-actions share one name — read `op` before calling.
- No implementation ships here. Bring a backend; the protocol is one method.

## What this is not

Not a wrapper around a hosted API. Not a model. Not a promise that calling it does
anything on its own. It is the typed seam plus the validation, which is the part that
stays correct even when the implementation changes.

## Tests

```bash
python -m pytest
```

```text
20 passed, 3 skipped in 0.11s
```

## Provenance

| field | value |
|---|---|
| capability | `tentacle_comfy_bridge` |
| domain | `creation` |
| handler | `tentacle_comfy_bridge` |
| safe | `True` (gated = `False`) |
| external | `False` |
| atlas page | [creation/tentacle_comfy_bridge](https://github.com/gbtweilai-star/gbt-caps/blob/main/caps/creation/tentacle_comfy_bridge.md) |
| full index | [https://github.com/gbtweilai-star/gbt-caps](https://github.com/gbtweilai-star/gbt-caps) |

The parent README says 348 capabilities; an AST pass over the registry enumerates
`341 + 0 unimplemented + 0 disabled = 341`. The extra 7 are historical duplicates.
Numbering here follows 341 and matches the `gbt-caps` index one-to-one.

## License

MIT.
