# Possible bug

This repo is intended to support a bug related to `yarn npm audit` where misleading information shows some package as culprit of a vulnerability when it is not the case.

## To reproduce

```sh
yarn
```

```sh
yarn npm audit --all --recursive
```

```text
├─ ansi-regex: 4.1.0
│  ├─ Issue:  Inefficient Regular Expression Complexity in chalk/ansi-regex
│  ├─ URL: https://github.com/advisories/GHSA-93q8-gq69-wqmw
│  ├─ Severity: moderate
│  ├─ Vulnerable Versions: >2.1.1 <5.0.1
│  ├─ Patched Versions: >=5.0.1
│  ├─ Via: eslint, webpack-dev-server
│  └─ Recommendation: Upgrade to version 5.0.1 or later
│
└─ glob-parent: 3.1.0
   ├─ Issue: Regular expression denial of service
   ├─ URL: https://github.com/advisories/GHSA-ww39-953v-wcq6
   ├─ Severity: high
   ├─ Vulnerable Versions: <5.1.2
   ├─ Patched Versions: >=5.1.2
   ├─ Via: eslint, webpack-dev-server
   └─ Recommendation: Upgrade to version 5.1.2 or later
```

As shown above there are 2 vulnerabilities, in both cases is shows `Via: eslint, webpack-dev-server`.
In such case, we do understand that `eslint` is culprit because pulled in such dangerous package.

But using `yarn why ansi-regex` and `yarn why glob-parent` we get:

```text
├─ gauge@npm:4.0.0
│  └─ ansi-regex@npm:5.0.1 (via npm:^5.0.1)
│
├─ strip-ansi@npm:3.0.1
│  └─ ansi-regex@npm:2.1.1 (via npm:^2.0.0)
│
├─ strip-ansi@npm:5.2.0
│  └─ ansi-regex@npm:4.1.0 (via npm:^4.1.0)
│
└─ strip-ansi@npm:6.0.1
   └─ ansi-regex@npm:5.0.1 (via npm:^5.0.1)

├─ chokidar@npm:2.1.8
│  └─ glob-parent@npm:3.1.0 (via npm:^3.1.0)
│
└─ eslint@npm:8.4.1
   └─ glob-parent@npm:6.0.2 (via npm:^6.0.1)
```

I have re-checked and do confirm: `eslint 8.4.1` > `strip-ansi 6.0.1` > `ansi-regex 5.0.1`.
I have re-checked and do confirm: `eslint 8.4.1` > `glob-parent 6.0.1`.

Then it should not be printed as responsible of any vulnerability.
