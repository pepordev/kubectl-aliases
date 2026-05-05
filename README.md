# kubectl-aliases

This repository contains [a script](generate_aliases.py) that generates thousands of
shell aliases for `kubectl`, built from permutations of short prefixes for the
binary, fixed namespaces, verbs, resource kinds, common flags, and trailing
arguments (`-n`, `-f`, `-l`).

The checked-in [`.kubectl_aliases`](.kubectl_aliases) file is the generated output
(currently on the order of **6,400+** `alias` lines). Regenerate it any time with:

```sh
python3 generate_aliases.py > .kubectl_aliases
```

When stdout is not a TTY (for example when redirecting as above), the script also
prepends the text from [`license_header`](license_header).

An example alias:

```sh
alias ksysgdepwslowidel='kubectl --namespace=kube-system get deployment --watch --show-labels -o=wide -l'
```

### Examples

```sh
alias k='kubectl'
alias wk='watch kubectl'

alias kg='kubectl get'
alias kgpo='kubectl get pods'

alias ksys='kubectl --namespace=kube-system'
alias ksysgpo='kubectl --namespace=kube-system get pods'

alias km='kubectl --namespace=monitoring'
alias kargo='kubectl --namespace=argocd'

alias krm='kubectl delete'
alias krmf='kubectl delete --recursive -f'
alias krmingl='kubectl delete ingress -l'
alias krmingall='kubectl delete ingress --all'

alias kgsvcoyaml='kubectl get service -o=yaml'
alias wkgsvc='watch kubectl get service'

alias kgf='kubectl get --recursive -f'
# ...
```

See the full list in [`.kubectl_aliases`](.kubectl_aliases).

### Installation

Download [`.kubectl_aliases`](https://raw.githubusercontent.com/pepordev/kubectl-aliases/main/.kubectl_aliases)
into your home directory (or clone this repo and copy the file), then source it
from `.bashrc` / `.zshrc`:

```sh
[ -f ~/.kubectl_aliases ] && source ~/.kubectl_aliases
```

**Print the full command before running it** (optional):

```sh
kubectl() { echo "+ kubectl $*">&2; command kubectl "$@"; }
```

### Regenerating

Edit the tables in [`generate_aliases.py`](generate_aliases.py), then:

```sh
python3 generate_aliases.py > .kubectl_aliases
```

### Syntax (short tokens)

- **Base**
  - **`k`** — `kubectl`
  - **`wk`** — `watch kubectl` (not combined with yaml/json/wide-only flows where disallowed by the generator)
- **Pinned namespaces** (optional; at most one per alias)
  - **`sys`** — `--namespace=kube-system`
  - **`m`** — `--namespace=monitoring`
  - **`es`** — `--namespace=external-secrets`
  - **`l`** — `--namespace=logging`
  - **`argo`** — `--namespace=argocd`
  - **`r`** — `--namespace=reloader`
  - **`cs`** — `--namespace=capsule-system`
  - **`sp`** — `--namespace=spegel`
- **Verbs / operations** (optional)
  - **`a`** — `apply --recursive -f`
  - **`ak`** — `apply -k` (not combined with the pinned namespaces above)
  - **`k`** — `kustomize` (same exclusion as `ak`)
  - **`ex`** — `exec -i -t`
  - **`lo`** — `logs -f`
  - **`lop`** — `logs -f -p`
  - **`p`** — `proxy` (cluster-scoped; not combined with pinned namespaces)
  - **`pf`** — `port-forward` (same)
  - **`g`** — `get`
  - **`d`** — `describe`
  - **`rm`** — `delete`
  - **`e`** — `edit`
  - **`run`** — `run --rm --restart=Never --image-pull-policy=IfNotPresent -i -t`
- **Resources** (optional; only where allowed for that verb in the script)
  - Core: **`po`** pods, **`dep`** deployment, **`st`** statefulset, **`ds`** daemonset, **`svc`** service, **`ing`** ingress, **`cm`** configmap, **`sec`** secret, **`pvc`** persistentvolumeclaim, **`pv`** persistentvolume, **`no`** nodes, **`ns`** namespaces
  - External Secrets: **`ss`** secretstores, **`es`** externalsecrets, **`ces`** clusterexternalsecrets, **`css`** clustersecretstores
  - Karpenter-style (cluster-scoped in the generator): **`nc`** nodeclaims, **`np`** nodepools
  - Other workload / config: **`so`** scaledobjects, **`cj`** cronjobs, **`job`** jobs, **`ntp`** networkpolicies
  - cert-manager: **`ch`** challenges, **`ord`** orders, **`cert`** certificates, **`cr`** certificaterequests, **`is`** issuers, **`cis`** clusterissuers
  - Monitoring (Prometheus Operator): **`amc`** alertmanagerconfigs, **`pr`** prometheusrules, **`sm`** servicemonitors
  - **`crd`** customresourcedefinitions (cluster-scoped)
  - Argo CD: **`app`** `applications.argoproj.io`
- **Flags** (optional; combinations obey incompatibility rules in code)
  - Output: **`oyaml`**, **`ojson`**, **`owide`**
  - **`sl`** — `--show-labels` (not with yaml/json; resource allowlist in script)
  - **`all`** — `--all-namespaces` on get/describe, or **`--all`** on delete (same short token, different expansion)
- **Trailing value flags** (mutually exclusive; kept at the end of the alias)
  - **`f`** — `--recursive -f` / filename
  - **`l`** — `-l` / label selector
  - **`n`** — `--namespace` (also allowed with **`lo`**, **`ex`**, **`pf`** where configured)

Exact pairing rules, resource allowlists, and incompatibilities live in the
`cmds`, `globs`, `ops`, `res`, `args`, and `positional_args` tables in
[`generate_aliases.py`](generate_aliases.py).

### FAQ

- **Does this slow down shell startup?** With thousands of aliases, cost depends
  on your shell and hardware. Measure sourcing time yourself if it matters (for
  example, time a subshell that only sources the file).

- **Can I add more resource types or namespaces?** Yes—extend the lists in
  [`generate_aliases.py`](generate_aliases.py) and regenerate. More aliases mean
  more definitions for the shell to parse at startup.

- **PowerShell?** There is a community fork oriented toward PowerShell
  [here](https://github.com/shanoor/kubectl-aliases-powershell).

### Authors

- Original idea and generator: [@ahmetb](https://twitter.com/ahmetb)
- This fork: extended namespaces, resources, and flags — [pepordev/kubectl-aliases](https://github.com/pepordev/kubectl-aliases)

---

This is not an official Google project.
