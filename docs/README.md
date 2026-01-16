---
title: Open-source, production infrastructure for AI agents
description: Production infrastructure that treats agents as native primitives, packaged as deployable units using the Agent Formation Schema.
doc-type: home
---

# MUXI Documentation

## Open-source, production infrastructure for AI agents

MUXI is **The Agent Server** - production infrastructure for running AI agents. It treats agents as native primitives, packaged as deployable units using the Agent Formation Schema.

[[boxed float-right]]

### Watch the Demo

[![image info](https://placehold.co/600x400)](http://google.com.au/)

From zero to a multi-agent AI system in under 5 minutes. See MUXI in action.

[[/boxed]]

- **Define everything in YAML** - Agents, tools, memory, knowledge, triggers – as a [deployable unit](concepts/formation-schema.md). Zero framework code.
- **Ship with one command** - `muxi deploy`. Done.
- **Let the orchestration roll** - Smart [Overlord](concepts/overlord.md) that routes, decomposes, clarifies, and coordinates.
- **Actually production-ready** - Multi-tenancy, observability, and enterprise features included, not bolted on.

### Think: “Docker for AI agents”.

Self-hosted, LLM-agnostic, Open-source.


[>] [Learn more about MUXI →](what-is-muxi.md)

---

## Get Started

:::: cols=3

(quickstart.md)[[card]]

#### Quickstart
Get your first agent running in 5 minutes.

[[/card]]

(how-it-works.md)[[card]]

#### How It Works
Understand the architecture and request flow.

[[/card]]

(examples/README.md)[[card]]

#### Examples
Real formations for common use cases.

[[/card]]

::::

---

## Install

[[tabs]]

[[tab macOS]]
```bash
brew install muxi-ai/tap/muxi
```
[[/tab]]

[[tab Linux]]
```bash
curl -fsSL https://muxi.org/install | sudo bash
```
[[/tab]]

[[tab Windows]]
```powershell
powershell -c "irm https://muxi.org/install | iex"
```
[[/tab]]

[[/tabs]]

[Full installation guide →](installation/README.md)

---

## Explore

:::: cols=2

(server/README.md)[[card]]

#### Server
Orchestration platform for running formations.

[[/card]]

(cli/README.md)[[card]]

#### CLI
Command-line tool for managing MUXI.

[[/card]]

(sdks/README.md)[[card]]

#### SDKs
Python, TypeScript, and Go client libraries.

[[/card]]

(registry/README.md)[[card]]

#### Registry
Discover and share formations.

[[/card]]

::::

---

## Learn

:::: cols=3

[[card]]

<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
    <path opacity="0.4" fill-rule="evenodd" clip-rule="evenodd" d="M12 3.17855C8.23203 3.17855 5.19444 6.17481 5.19444 9.85024C5.19444 11.1075 5.54828 12.2813 6.16379 13.2842C6.44299 13.7391 6.29751 14.3323 5.83886 14.6093C5.38021 14.8862 4.78207 14.7419 4.50288 14.287C3.70768 12.9913 3.25 11.4721 3.25 9.85024C3.25 5.09122 7.17688 1.25 12 1.25C16.8231 1.25 20.75 5.09122 20.75 9.85024C20.75 11.4721 20.2923 12.9913 19.4971 14.287C19.2179 14.7419 18.6198 14.8862 18.1611 14.6093C17.7025 14.3323 17.557 13.7391 17.8362 13.2842C18.4517 12.2813 18.8056 11.1075 18.8056 9.85024C18.8056 6.17481 15.768 3.17855 12 3.17855Z" fill="currentColor"></path>
    <path opacity="0.4" d="M15.8432 16C15.9736 16 16.1022 16 16.2105 16.005C16.325 16.0102 16.4787 16.0228 16.6389 16.0712C17.1374 16.2217 17.4733 16.5863 17.4987 16.9998C17.5069 17.1326 17.4739 17.2496 17.4441 17.3357C17.1127 19.0176 16.4285 19.4999 15.8431 19.4999H8.1569C7.57151 19.4999 6.8873 19.0176 6.55585 17.3357C6.52614 17.2496 6.49309 17.1326 6.50126 16.9998C6.5267 16.5863 6.8626 16.2217 7.3611 16.0712C7.52127 16.0228 7.67503 16.0102 7.78946 16.005C7.89781 16 8.02637 16 8.15678 16H8.15684H15.8432H15.8432Z" fill="currentColor"></path>
    <path d="M10.3321 22.6513C10.6774 22.7518 11.0856 22.7518 11.9019 22.7518C12.7182 22.7518 13.1264 22.7518 13.4717 22.6513C14.0057 22.4958 14.4551 22.1704 14.7324 21.7384C14.8356 21.5776 14.906 21.3923 14.9776 21.1291C15.0674 20.7991 15.1123 20.6341 15.0223 20.5163C14.9322 20.3984 14.7551 20.3984 14.4009 20.3984H9.40295C9.04873 20.3984 8.87162 20.3984 8.78156 20.5163C8.69151 20.6341 8.73641 20.7991 8.82623 21.1291C8.89786 21.3923 8.96819 21.5776 9.07142 21.7384C9.34876 22.1704 9.79812 22.4958 10.3321 22.6513Z" fill="currentColor"></path>
    <path fill-rule="evenodd" clip-rule="evenodd" d="M10.154 6.25C9.53997 6.25 8.94823 6.62604 8.74718 7.24834L7.28652 11.7694C7.15918 12.1636 7.37548 12.5863 7.76963 12.7137C8.16378 12.841 8.58654 12.6247 8.71388 12.2306L9.03068 11.25H11.2774L11.5942 12.2306C11.7216 12.6247 12.1443 12.841 12.5385 12.7137C12.9326 12.5863 13.1489 12.1636 13.0216 11.7694L11.5609 7.24834C11.3599 6.62604 10.7681 6.25 10.154 6.25ZM9.51465 9.74955L10.1534 7.77246L10.7922 9.74955H9.51465Z" fill="currentColor"></path>
    <path d="M15.7502 7C15.7502 6.58579 15.4144 6.25 15.0002 6.25C14.586 6.25 14.2502 6.58579 14.2502 7V12C14.2502 12.4142 14.586 12.75 15.0002 12.75C15.4144 12.75 15.7502 12.4142 15.7502 12V7Z" fill="currentColor"></path>
</svg>


### Core Concepts

- [Architecture](concepts/architecture.md)
- [The Overlord](concepts/overlord.md)
- [Formation Schema](concepts/formation-schema.md)
- [Memory System](concepts/memory-system.md)
- [Tools & MCP](concepts/tools-and-mcp.md)

[[/card]]

[[card]]

<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
    <circle opacity="0.3" cx="12" cy="12" r="10" fill="currentColor"></circle>
    <path d="M10.0808 2C5.47023 2.9359 2 7.01218 2 11.899C2 17.4776 6.52238 22 12.101 22C16.9878 22 21.0641 18.5298 22 13.9192" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"></path>
    <path d="M18.9375 18C19.3216 17.9166 19.6771 17.784 20 17.603M14.6875 17.3406C15.2831 17.6015 15.8576 17.7948 16.4051 17.9218M10.8546 14.9477C11.2681 15.238 11.71 15.5861 12.1403 15.8865M3 13.825C3.32234 13.6675 3.67031 13.4868 4.0625 13.3321M6.45105 13C7.01293 13.0624 7.64301 13.2226 8.35743 13.5232" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"></path>
    <path d="M17.488 13.6202C17.223 13.8638 16.8687 14 16.5001 14C16.1315 14 15.7773 13.8638 15.5123 13.6202C13.0855 11.3756 9.83336 8.86815 11.4193 5.2278C12.2769 3.25949 14.3353 2 16.5001 2C18.6649 2 20.7234 3.25949 21.5809 5.2278C23.1649 8.86356 19.9207 11.3833 17.488 13.6202Z" fill="white"></path>
    <path d="M17.488 13.6202C17.223 13.8638 16.8687 14 16.5001 14C16.1315 14 15.7773 13.8638 15.5123 13.6202C13.0855 11.3756 9.83336 8.86815 11.4193 5.2278C12.2769 3.25949 14.3353 2 16.5001 2C18.6649 2 20.7234 3.25949 21.5809 5.2278C23.1649 8.86356 19.9207 11.3833 17.488 13.6202Z" stroke="currentColor" stroke-width="1.5"></path>
    <path d="M18 7.5C18 6.67157 17.3284 6 16.5 6C15.6716 6 15 6.67157 15 7.5C15 8.32843 15.6716 9 16.5 9C17.3284 9 18 8.32843 18 7.5Z" fill="white"></path>
    <path d="M18 7.5C18 6.67157 17.3284 6 16.5 6C15.6716 6 15 6.67157 15 7.5C15 8.32843 15.6716 9 16.5 9C17.3284 9 18 8.32843 18 7.5Z" stroke="currentColor" stroke-width="1.5"></path>
</svg>

#### Guides

- [Add Tools (MCP)](guides/add-mcp-tools.md)
- [Add Memory](guides/add-memory.md)
- [Build Multi-Agent Teams](guides/build-multi-agent-systems.md)
- [Deploy to Production](guides/deploy-to-production.md)
- [Troubleshooting](guides/troubleshooting.md)

[[/card]]

[[card]]

<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
    <path opacity="0.4" d="M6.05352 3.80973L6.1133 3.82048C9.65131 4.45669 11.7782 6.27394 12.5432 7.07749C12.676 7.21693 12.75 7.40209 12.75 7.59462V18.5731C13.5729 17.3964 14.7329 16.0717 16.2446 15.2202C16.626 15.0054 16.8768 14.8636 17.0522 14.7479C17.151 14.6827 17.1978 14.643 17.2165 14.6262C17.2214 14.6064 17.2294 14.5659 17.2358 14.493C17.2493 14.3377 17.25 14.1267 17.25 13.7805V5.28906C17.25 5.04965 17.3643 4.82463 17.5576 4.68342C18.1367 4.26043 18.8714 3.91808 19.5838 3.76196C20.2556 3.61471 21.1187 3.59068 21.751 4.10239C22.2993 4.54611 22.5409 5.12306 22.6499 5.78422C22.7501 6.39193 22.7501 7.1543 22.75 8.04917L22.75 14.964C22.75 15.7916 22.75 16.4773 22.6872 17.0211C22.6219 17.5865 22.4788 18.1111 22.1073 18.5454C21.7259 18.9912 21.1732 19.2215 20.5715 19.3904C19.9696 19.5593 19.1767 19.7019 18.1923 19.8789L18.1923 19.8789L18.1522 19.8861C16.4055 20.2002 15.0588 20.6978 14.1014 21.1857L14.0831 21.195L14.083 21.1951C13.5939 21.4444 13.2033 21.6434 12.9047 21.7791C12.7526 21.8481 12.6067 21.9089 12.4716 21.9533C12.3444 21.9951 12.1786 22.0391 12 22.0391C11.8214 22.0391 11.6556 21.9951 11.5284 21.9533C11.3933 21.9089 11.2474 21.8481 11.0953 21.7791C10.7967 21.6434 10.4061 21.4444 9.91701 21.1951L9.9169 21.195L9.89862 21.1857C8.94122 20.6978 7.59451 20.2002 5.84783 19.8861L5.80773 19.8789L5.80768 19.8789C4.82329 19.7019 4.03038 19.5593 3.42854 19.3904C2.82678 19.2215 2.27407 18.9912 1.89269 18.5454C1.52123 18.1111 1.37808 17.5865 1.31278 17.0211C1.24997 16.4773 1.24998 15.7916 1.25 14.964L1.25 8.11039L1.25 8.04917C1.24995 7.1543 1.24991 6.39193 1.35009 5.78422C1.45909 5.12306 1.70069 4.54611 2.24897 4.10239C2.77662 3.67538 3.35126 3.53518 3.99087 3.53914C4.56937 3.54273 5.2584 3.66668 6.05352 3.80973Z" fill="currentColor"></path>
    <path fill-rule="evenodd" clip-rule="evenodd" d="M16.9516 3.49188C16.7664 3.56957 16.5252 3.73672 16.0682 4.06377C14.3161 5.31781 13.2249 7.14093 12.75 7.98538V18.5743C13.5729 17.3975 14.7329 16.0728 16.2446 15.2214C16.626 15.0066 16.8768 14.8648 17.0522 14.749C17.151 14.6838 17.1978 14.6442 17.2165 14.6274C17.2214 14.6075 17.2294 14.567 17.2358 14.4942C17.2493 14.3389 17.25 14.1278 17.25 13.7816V4.94167C17.25 4.29323 17.2465 3.91985 17.1964 3.6666C17.1621 3.49307 17.1298 3.48571 17.1176 3.48294C17.1162 3.48262 17.1151 3.48237 17.1143 3.48194C17.0892 3.46906 17.0741 3.46431 17.0675 3.46257C17.0616 3.46101 17.059 3.46095 17.0574 3.46094C17.0552 3.46092 17.026 3.46066 16.9516 3.49188ZM17.2117 14.6433C17.2117 14.6433 17.2118 14.643 17.2121 14.6424L17.2117 14.6433ZM17.0689 1.96098C17.3313 1.96298 17.5745 2.03196 17.8 2.14785C18.3346 2.42264 18.5704 2.88285 18.6679 3.37536C18.7503 3.79158 18.7501 4.31718 18.75 4.87621C18.75 4.89798 18.75 4.9198 18.75 4.94167L18.75 13.8109C18.75 14.1185 18.75 14.3961 18.7301 14.6247C18.7089 14.8672 18.6603 15.1364 18.5109 15.392C18.3501 15.6671 18.0968 15.8569 17.8784 16.001C17.6534 16.1495 17.3556 16.3172 17.0047 16.5148L16.9807 16.5283C14.7588 17.7798 13.3923 20.2984 12.7916 21.4055C12.7408 21.4991 12.6955 21.5826 12.6556 21.6544C12.4896 21.9532 12.1427 22.102 11.8119 22.0162C11.481 21.9305 11.25 21.632 11.25 21.2902V7.79021C11.25 7.66275 11.2825 7.5374 11.3444 7.42598L11.3513 7.41348C11.7741 6.6525 13.028 4.39518 15.1952 2.84402C15.213 2.83126 15.2308 2.81849 15.2487 2.80571C15.6291 2.53319 16.0103 2.26009 16.3714 2.10863C16.578 2.02197 16.8112 1.95902 17.0689 1.96098Z" fill="currentColor"></path>
</svg>

#### Reference

- [Formation Schema](reference/formation-schema.md)
- [API Reference](reference/api-reference.md)
- [CLI Cheatsheet](cli/cheatsheet.md)
- [Deep Dives](deep-dives/README.md)
- [How Do I...?](faq.md)

[[/card]]

::::

---

## Popular Pages

[+] [Quickstart](quickstart.md) - 5 minutes to your first agent
[+] [Examples](examples/README.md) - See real formations
[+] [Formation Schema](concepts/formation-schema.md) - YAML configuration
[+] [Add Tools (MCP)](guides/add-mcp-tools.md) - Give agents capabilities
[+] [Deploy to Production](guides/deploy-to-production.md) - Go live
