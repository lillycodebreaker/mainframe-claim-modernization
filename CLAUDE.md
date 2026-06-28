<!-- SPECKIT START -->
For additional context about technologies to be used, project structure,
shell commands, and other important information, read the current plan:
`specs/001-pbm-rx-modernization/plan.md`

Active feature: PBM Rx Claim Adjudication Mainframe Modernization Demo
- Spec: `specs/001-pbm-rx-modernization/spec.md`
- Plan: `specs/001-pbm-rx-modernization/plan.md`
- Design artifacts: `research.md`, `data-model.md`, `quickstart.md`, `contracts/`

Stack: Node.js 20 + TypeScript (modern-node-api/), Python 3.11 (python-validation/),
React + Vite (dashboard/). Docs in docs/, platform/CI in platform/.

Hard rules: `legacy-reference/` is READ-ONLY golden master (never modify). Synthetic
data only — no PHI/PII. Generated code follows `style-reference/modern-node-api-style`
and `style-reference/python-validation-style`.
<!-- SPECKIT END -->
