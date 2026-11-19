# Sidebar plugin

Bundles the Sidebar case-law connector with four skills for litigation work in Claude:

- **potential-claims**: which claims a client has or faces, from the facts: elements, limitations period and last day to file, remedies, defenses and viability per claim, with rejected claims kept.
- **motion-to-dismiss**: claim-by-claim, element-by-element analysis of a motion to dismiss from either side.
- **summary-judgment**: readiness, exposure, and a statement of facts or a response to one, checked against the record.
- **brief-teardown**: checks the other side's brief: citations, quotes, pins, good law, overstated holdings, later limiting cases, and omissions.

Each skill keeps its work as a markdown file in the Claude Project (`claims/…`, `motions/…`, `briefs/…`). It finds that
file on the next run, updates it, records what changed, and re-checks every citation before saving. The skills
ask for missing documents before they start. Standards, elements and limitations periods come from Sidebar's `legal_standard`,
`claim_elements` and `limitations_period` tools, and deadlines from `compute_deadline`, so they are not
written from memory.

Install in Claude: **Plugins → Add → Add marketplace → Add from a repository**, enter
`wardethan2000-eng/sidebar-plugin`, then add **Sidebar** from **Discover**. Connect the Sidebar connector on the
plugin's Connectors tab and sign in.

In Claude Code: `/plugin install sidebar --marketplace wardethan2000-eng/sidebar-plugin`
