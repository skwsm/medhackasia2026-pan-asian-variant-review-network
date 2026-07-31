---
title: "Building a Collaborative Rare Disease Variant Review Network Across Asia at MedHackathon Asia 2026"
title_short: "MedHackathon Asia 2026: pan-asian-variant-review-network"
tags:
  - rare disease
  - variant interpretation
  - genomic medicine
  - expert panel
  - Asia
authors:
  - name: Toyofumi Fujiwara
    orcid: 0000-0002-0170-9172
    affiliation: 1
  - name: George Devasia
    orcid: 
    affiliation: 2
  - name: Rutharra Ghayadthri Manisekaran
    orcid: 0009-0008-6310-9974
    affiliation: 3
  - name: Vasanthan Jayakumar
    orcid: 0000-0002-6067-4184
    affiliation: 4
  - name: Yuko Kitano 
    orcid: 
    affiliation: 5
  - name: Yosuke Kawai
    orcid: 0000-0003-0666-1224
    affiliation: 1
  - name: Shuichi Kawashima
    orcid: 0000-0001-7883-3756
    affiliation: 1
  - name: Francis A. Tablizo
    orcid: 
    affiliation: 6
  - name: Shoichiro Takahashi
    orcid: 
    affiliation: 8
  - name: Piyakrit Wongboonchai
    orcid: 
    affiliation: 9
affiliations:
  - name: National Institute of Genetics, Japan
    index: 1
  - name: NTU Lee Kong Chian School of Medicine, Singapore
    index: 2
  - name: National Cancer Centre Singapore
    index: 3
  - name: Kitasato University, Japan
    index: 4
  - name: BITS.Co.,Ltd. 
    index: 5
  - name: Philippine Genome Center
    index: 6
  - name: University of the Philippines
    index: 7
  - name: Trinet Corporation, Japan
    index: 8
  - name: Genomic Medicine Centre, Department of Medical Sciences, Ministry of Public Health Thailand
    index: 9
date: "31 July 2026"
cito-bibliography: paper.bib
event: "MedHackathonAsia2026"
biohackathon_name: "MedHackathon Asia 2026"
biohackathon_url: "https://medhackathon.github.io/2026/"
biohackathon_location: "Singapore, 2026"
group: ""
git_url: ""
authors_short: 'Toyofumi Fujiwara \\emph{et al.}'
---

# Abstract

Genome and exome sequencing can identify tens of thousands of variants in a single rare disease case, but only a small subset can be reviewed in depth by a multidisciplinary expert panel. The challenge is not only to filter a Variant Call Format (VCF) file. It is also to integrate population-specific allele frequencies, molecular consequences, inheritance, phenotype, literature and functional evidence; preserve uncertainty and reviewer reasoning; and enable knowledge gained in one institution or country to be reused elsewhere without centralizing identifiable patient data.

At MedHackathon Asia 2026, participants developed the design of a collaborative rare disease variant review network across Asia. The work was organized into three connected workstreams: variant prioritization and ACMG/AMP-style evidence preparation; phenotype extraction and expert validation; and secure expert-review system design. The proposed workflow retains the full VCF and clinical record in a local or trusted environment, produces a structured shortlist of approximately 10-20 candidate variants, and transfers only the information required for authorized review. Experts then assess a smaller set of high-priority variants, record accepted, rejected and pending evidence, preserve individual positions and dissent, and generate a review outcome. Reusable, appropriately governed variant-level evidence can subsequently be shared across institutions and countries.

The hackathon clarified which interpretation tasks may be automated, which should be semi-automated with mandatory confirmation, and which depend primarily on clinical or family-level judgment. It also identified regional differences in population resources, clinical-data availability, interpretation practice, language and data-access constraints. System requirements were specified for cloud deployment, OpenID Connect authentication, per-case roles, authorization, conflict-safe concurrent editing, individual consensus tracking and detailed audit logging. The existing ExpertBoard prototype provides an initial foundation but does not yet implement the complete review workflow.

This report presents the resulting architecture, requirements and implementation roadmap. It is a design and early-prototype report rather than a clinical validation study. Future work will include reproducible workflow comparisons, structured evidence exchange, multicountry pilot panels, evaluation of inter-panel agreement and disagreement, and governance mechanisms that support regional collaboration while preserving local responsibility.

# Introduction

Rare and undiagnosed disease analysis increasingly uses whole-genome sequencing (WGS) or whole-exome sequencing (WES). After alignment and variant calling, a case may contain tens of thousands of single-nucleotide and small insertion/deletion variants, and potentially additional copy-number, structural, repeat and mitochondrial variants. The clinically relevant task is therefore to move from a large technical call set to a small, explainable group of candidates that can be reviewed in the context of a patient's phenotype, family history and disease mechanism.

Variant interpretation is supported by resources such as gnomAD, ClinVar, ClinGen, the Ensembl Variant Effect Predictor (VEP), the Human Phenotype Ontology (HPO) and Online Mendelian Inheritance in Man (OMIM) [@Karczewski:2020; @Landrum:2025; @Rehm:2015; @McLaren:2016; @Kohler:2021; @Amberger:2015]. The ACMG/AMP framework provides a widely used structure for combining evidence toward pathogenic, likely pathogenic, uncertain, likely benign or benign classifications [@Richards:2015]. Subsequent work has refined individual criteria, introduced quantitative interpretations of evidence strength and calibrated computational evidence [@Tavtigian:2018; @Pejaver:2022]. These developments enable greater consistency, but they do not eliminate the need for expert judgment.

Population context is especially important. Allele frequencies and disease-associated variants can differ among ancestry groups, while many widely used resources remain uneven in their representation of Asian populations. Singaporean genome studies have demonstrated the value of ancestrally diverse regional cohorts for interpreting clinically relevant variants [@Chan:2022]. Japanese resources such as jMorp and TogoVar provide population and variant information relevant to Japanese cases [@Tadaka:2024; @Mitsuhashi:2022], while T-REx provides a Thai reference exome resource [@Shotelersuk:2021]. These resources are complementary rather than interchangeable. Their availability, access mechanisms, sample composition and clinical context differ.

MedHackathon Asia was created as a working forum for practical regional collaboration in genomic and health data. MedHackathon Asia 2026, held in Singapore from 27-31 July 2026, included projects on variant-analysis harmonization, federated environments, population-aware interpretation, genomic data governance and regional resource discovery [@MedHackathonAsia:2026]. It builds on the collaborative framework developed at the first MedHackathon Asia in 2025 [@MedHackathonCommunity:2026].

During the 2026 meeting, participants proposed a network that connects local variant analysis, phenotype review, multidisciplinary expert assessment and reusable regional knowledge. The objective is not to create a single authority that issues an "Asian" classification. Instead, panels in different countries or institutions should be able to examine the same variant, see the evidence available to other panels, retain their own conclusions and display similarities and differences side by side.

# Project objectives and design principles

The long-term objective is to establish an academically governed platform that can:

1. accept or reference VCF, phenotype and other case information;
2. reduce a large call set to a manageable candidate list;
3. organize ACMG/AMP-style evidence without automatically imposing a final classification;
4. support multidisciplinary and geographically distributed review;
5. generate a patient-specific review report within an authorized environment;
6. retain provenance, reviewer reasoning, uncertainty and audit history; and
7. share appropriate variant-level evidence and panel assessments across Asia.

Five design principles guided the hackathon work.

**Local control of sensitive data.** Full genomic and clinical records remain within the originating institution, a trusted research environment or another approved local infrastructure. The network should exchange the minimum information required for a defined task.

**Human-governed interpretation.** Automation is used to retrieve, calculate, summarize and propose. Final evidence acceptance and case interpretation remain the responsibility of qualified reviewers.

**Visible provenance and uncertainty.** Every evidence item should record its source, version, date, scope and reviewer status. Rejected and pending evidence are retained rather than silently removed.

**Plurality rather than forced consensus.** The platform records both individual reviewer positions and panel-level outcomes. Country- or institution-specific assessments can remain different when evidence or interpretation differs.

**Incremental implementation.** Variant prioritization, phenotype review, authentication, expert workflow and regional exchange can be developed as interoperable modules rather than requiring an immediate end-to-end clinical system.

# Hackathon organization and process

The work was divided into three connected workstreams (Table 1).

Table 1. Workstreams and principal outputs at MedHackathon Asia 2026.

| Workstream | Questions addressed | Principal outputs |
|---|---|---|
| Variant prioritization and evidence | How should candidate variants be selected, annotated and mapped to ACMG/AMP-style evidence? Which tasks can be automated? | Input scenarios, evidence matrix, automation boundaries, regional comparison and prioritization requirements |
| Phenotype review | How should clinical narratives and HPO terms be extracted, corrected and connected to variant evidence? | Prototype phenotype-review workflow, accept/reject/pending states, disease suggestions and missing-information requirements |
| System design and implementation | How can distributed experts securely review the same case and preserve decisions? | Deployment, authentication, authorization, role, concurrency, consensus and audit-log requirements |

The group used discussion-derived workflow descriptions from several participating jurisdictions, an existing ExpertBoard prototype and sample VCF scenarios. No patient-level diagnostic performance study was conducted. Country and institutional descriptions in this report reflect participant discussions and must not be interpreted as official national policy or a comprehensive survey.

# Integrated workflow architecture

Figure 1 presents the proposed separation among local analysis, restricted expert review and controlled release.

Figure 1. Proposed integrated workflow. Patient-level VCF and clinical notes are processed in an on-premises or trusted environment. A structured shortlist is transferred to an authorized expert-review environment, and only approved, appropriately governed results are released.

![Figure 1. Proposed integrated workflow from local analysis to restricted expert review and controlled knowledge release.](figures/integrated_workflow.png)

## Local variant and phenotype processing

The starting point is a VCF generated by an upstream WGS/WES pipeline. Upstream alignment, variant calling and primary quality control are not replaced by this project. The local component performs post-calling normalization, annotation, filtering and prioritization.

The variant workstream proposed several input scenarios to ensure that the system does not assume a single clinical pattern:

- a single patient with a known phenotype;
- a single patient with an unknown or incomplete phenotype;
- multiple patients sharing a phenotype;
- multiple patients without a shared or known phenotype;
- a list of variants within one gene;
- a condition-based multigene analysis; and
- separate treatment of variant classes for which different evidence rules apply.

Candidate annotations include normalized variant identity, genome assembly, transcript, molecular consequence, quality metrics, population frequency, prior clinical assertions, gene-disease association, inheritance, computational predictions, literature, functional evidence and phenotype relevance. The intended output is a shortlist of approximately 10-20 variants. This is an operational target for review, not a universal biological threshold.

Clinical notes form a parallel path. For the initial prototype, participants recommended accepting precomputed HPO terms rather than attempting unrestricted automated extraction from electronic health records. Future versions may use language models or other natural-language processing methods to propose HPO terms, but each proposal should preserve the supporting text span, confidence and provenance and should remain subject to expert confirmation. PubCaseFinder provides an existing phenotype-driven resource that can support disease and case prioritization [@Fujiwara:2018].

## Restricted expert review

The prioritized candidate package is transferred to a secure review environment accessible only to authorized participants. The transfer should contain the minimum phenotype and case context needed for interpretation, together with the full provenance of the prioritization process.

The expert panel may include a case chair, clinical reviewer, bioinformatician, laboratory scientist, genetic counselor, case coordinator and external consultant. Each role is assigned per case rather than permanently to a user account, because a reviewer may chair one case and advise on another.

For each candidate, the workspace should support:

- viewing structured annotations and source versions;
- proposing ACMG/AMP-style evidence criteria and strengths;
- accepting, rejecting, modifying or leaving evidence pending;
- adding literature, functional, segregation and phenotype evidence;
- recording uncertainty and requests for additional information;
- storing each reviewer's position and notes;
- preserving disagreement and abstention;
- finalizing a panel outcome through an authorized role; and
- generating an audit-ready report.

The system must distinguish an automated proposal, an individual reviewer's assessment, a panel summary and a formal clinical report issued by an accredited service. These objects have different authority and should not be presented interchangeably.

## Controlled regional knowledge sharing

The final stage creates reusable knowledge objects from reviewed evidence. Two broad information classes were identified.

The first consists of variant-level evidence that is not inherently patient-specific, including population-frequency observations, literature, functional evidence, transcript consequences, gene-disease relationships and evidence-code assessments with provenance.

The second consists of expert-reviewed annotations derived from cases. Depending on consent, institutional policy and re-identification risk, this may include the panel assessment, accepted and rejected evidence, unresolved questions and recommended conditions for re-evaluation. Patient-specific phenotype combinations, dates, family structures and free text require particular caution because rare disease cases may remain re-identifiable even after removal of direct identifiers.

A shared record should identify the reviewing panel or jurisdiction, disease context, inheritance model, transcript, guideline version, date, evidence sources and review status. The platform can then show assessments from Japan, Singapore, the Philippines, Thailand and future participating jurisdictions side by side. Differences are treated as informative results that require explanation, not as errors that must automatically be collapsed.

# Variant evidence and the boundary of automation

A major hackathon output was an explicit separation between evidence tasks that can be automated, tasks that can be computed but require expert confirmation, and tasks that depend primarily on clinical judgment (Table 2).

Table 2. Proposed automation boundary for ACMG/AMP-style evidence preparation.

| Automation level | Example evidence tasks | Human responsibility |
|---|---|---|
| Largely automatable | Variant normalization and type; retrieval of prior assertions; same amino-acid substitutions; calibrated computational predictions; basic transcript consequence; rule-based candidate generation | Verify correct transcript, disease mechanism, tool calibration, data version and applicability |
| Semi-automated | Loss-of-function assessment; population-frequency thresholds; mutational-domain evidence; functional-study retrieval; case-count or prevalence evidence; in-frame indel assessment | Confirm disease mechanism, inheritance, penetrance, assay validity, independence of cases and appropriate evidence strength |
| Primarily expert-derived | De novo status; phase and segregation; phenotype specificity; alternative molecular explanations; family relationships; final functional interpretation | Review primary clinical and laboratory data, assess uncertainty and document rationale |

Loss-of-function evidence illustrates the distinction. A tool such as AutoPVS1 can implement a decision framework and produce a preliminary PVS1 assessment [@Xiang:2020], but reviewers must still confirm the relevant transcript, whether loss of function is a known disease mechanism, expected nonsense-mediated decay and other gene- and disease-specific considerations.

Population evidence can be calculated from global and regional databases, but the meaning of an observed frequency depends on disease prevalence, penetrance, inheritance, technical quality and population composition. A locally common variant can appear misleadingly rare in a global dataset, while a variant absent from a regional dataset may simply be insufficiently sampled. Therefore, candidate BA1, BS1, BS2 or PM2 evidence should be proposed with the underlying counts and assumptions, not represented as an unexplained binary flag.

Computational evidence is suitable for automated display and calibrated evidence suggestions, but correlated predictors must not be counted as independent evidence. ClinGen calibration work provides a basis for assigning computational evidence strengths under defined conditions [@Pejaver:2022]. The system should store the specific model, version, threshold and direction of evidence used.

Family segregation, de novo status and phase are not reliably inferred from a single proband VCF. They require pedigree and sample-relationship validation, parental or relative testing and, where applicable, quantitative segregation assessment. Likewise, phenotype specificity cannot be reduced to a similarity score alone. A clinician must evaluate onset, negative findings, phenocopies, disease mechanism and alternative explanations.

The proposed AI role is consequently assistive. AI may retrieve candidate publications, summarize evidence, propose HPO terms or pre-populate an evidence form. It should not silently create evidence, hide contradictory sources or finalize a classification.

# Phenotype review workflow

Phenotype review is the bridge between the patient's clinical story and variant interpretation. Incomplete or incorrectly encoded phenotypes can cause a causal variant to be ranked too low or can make an incidental variant appear relevant.

The phenotype workstream proposed the following workflow:

1. receive precomputed HPO terms and the clinical text or structured source from which they were derived;
2. display all candidate phenotype terms with provenance;
3. allow experts to mark terms as accepted, rejected or pending;
4. preserve age of onset, severity, temporal course and explicitly absent findings where available;
5. generate candidate diseases using resources such as OMIM, ClinGen and phenotype-driven tools;
6. identify missing examinations, tests or family information that would be most informative;
7. connect the reviewed phenotype profile to candidate genes and variants; and
8. allow phenotype evidence such as PP4 to be proposed but only applied after clinical confirmation.

Rejected terms should remain visible in the audit history because their removal can materially alter prioritization. Pending terms are also important: a feature may be plausible but unconfirmed, age-dependent or obscured by limited records.

In future versions, language models may assist with extraction from multilingual clinical notes. Such use should be evaluated separately for each language and clinical setting. Extracted terms require source-linked review, and a generated disease suggestion should not be treated as a diagnosis.

# Regional requirements and complementary resources

Hackathon participants compared practical needs in Singapore, Japan, the Philippines and Thailand. Table 3 intentionally summarizes only high-level, discussion-derived characteristics. It is not an authoritative description of national practice.

Table 3. Provisional regional considerations identified in hackathon discussions.

| Consideration | Singapore | Japan | Philippines | Thailand |
|---|---|---|---|---|
| Population context | Multi-ancestry population including Chinese, Malay and Indian groups | Predominantly Japanese population with regional substructure | Diverse Filipino and admixed populations | Diverse Thai populations and neighboring ancestry contributions |
| Examples of population resources | SG10K and PRECISE-SG100K | jMorp and TogoVar | Resources under development or institution-specific | T-REx and participant-reported WGS resources |
| Commonly identified need | Variant filtering and prioritization across research and clinical contexts | Integration and annotation of domestic variant evidence | Prioritization and development of shareable resources | Prioritization using population-aware frequency data |
| Clinical phenotype availability | Often differs between research and clinical datasets | Available in some clinical and research collaborations | Frequently limited to internal collaborators or referred testing | Often available primarily for clinical cases |
| Cross-cutting constraint | Managed access and separation of research from clinical use | Language and integration across domestic resources | Infrastructure and resource maturity | Access, validation and governance requirements |

The comparison highlighted a general pattern. Population cohorts can provide frequency, recurrence and ancestry context but may lack detailed phenotypes. Clinical programs can provide rich phenotype, segregation and disease-mechanism evidence but may have smaller local control datasets. Regional collaboration can connect these complementary forms of evidence without implying that patient-level datasets must be pooled.

Figure 2 illustrates a hypothetical feedback loop. The displayed reclassification is an example only and was not a validated hackathon result.

Figure 2. Conceptual regional feedback loop between population-based variant evaluation and phenotype-driven expert review.

![Figure 2. Conceptual feedback between population cohorts, expert review and a shared variant knowledge resource.](figures/regional_feedback_loop.png)

A variant classified as uncertain in a population dataset may become more interpretable when a clinical panel contributes phenotype, segregation or functional evidence. Conversely, a clinical panel may revise the weight of population evidence when regional frequencies become available. Each panel should independently evaluate the shared evidence and document why its conclusion agrees with or differs from another panel.

# ExpertBoard prototype and review-system requirements

ExpertBoard is an experimental standalone prototype for AI-assisted complex case review [@ExpertBoard:2026]. Its current repository provides reusable boards, case-review rooms, a multidisciplinary role model, phenotype and variant views, and database tables for cases, membership, comments, decisions and audit logs. At the time of the hackathon, the prototype supported viewing boards and cases and creating demonstration content, while the complete authentication and decision workflow remained to be implemented.

The system-design workstream refined the following requirements.

## Deployment and tenancy

Experts need access from different institutions and countries, so the review application should be cloud-hosted or deployed in an equivalently reachable secure environment. This does not imply that full VCFs or clinical records must leave local infrastructure. A single shared application instance is sufficient for an initial pilot; separate institutional deployments and federation can be evaluated later.

## Authentication and future federation

Authentication should be delegated to an established identity provider through OpenID Connect rather than implemented within the application. The proposed initial implementation uses Keycloak [@Keycloak:2026]. Accounts are provisioned by an administrator, sessions must be revocable, repeated failures must be rate-limited and two-factor authentication should be available.

The architecture should not prevent later adoption of GA4GH standards. GA4GH Passports represent machine-readable user roles and permissions through visas [@Voisin:2021], while the Data Use Ontology provides computable terms for permitted data uses [@Lawson:2021]. These standards are relevant to future cross-institutional and cross-border authorization, although they are outside the initial MVP.

## Per-case roles and authorization

Roles are assigned per case. All authorized case participants may read and comment, but structured actions should be restricted. For example, a laboratory scientist may edit functional evidence, a clinical reviewer may validate phenotype evidence and a case chair may move the case through its lifecycle.

The case lifecycle requires a single controlled vocabulary. The hackathon notes identified two partially overlapping status models in the concept and existing code. Resolving these into authoritative states and permitted transitions is a prerequisite for implementation.

Finalizing a panel outcome is a high-stakes action. It should be limited to the case chair or another explicitly authorized role and should require an additional confirmation step. Finalization must not erase individual positions or unresolved evidence.

## Concurrent work

Multiple experts should be able to work on the same case without silently overwriting one another. Full Google-Docs-style co-editing is unnecessary for the MVP because most actions are structured and target separate objects. Optimistic concurrency control or version checks can reject conflicting writes and ask the user to reload or reconcile changes.

Joint editing of free-text minutes could be added later as a separately scoped feature.

## Consensus and dissent

The system must record each core reviewer's position, such as agree, disagree or abstain, together with an optional note. An aggregate vote alone is insufficient. Notes attached to agreement and abstention are as important as formal dissent and must remain available after closure.

"Consensus" should be understood as a recorded panel process, not as a requirement that every reviewer or every country reach the same conclusion. A panel may close a case with no consensus or with a request for additional data.

## Audit logging

Every meaningful write action should record who acted, when, what object changed and the before-and-after values. Login, logout, failed authentication and denied access attempts should also be logged. An entry that merely states "updated" is not sufficient for reconstructing a disputed decision.

Audit records require protection from routine modification and an appropriate retention policy. The audit trail is essential for research reproducibility, security monitoring and any future clinical-quality evaluation.

# Proposed interoperable information objects

The project distinguishes three information packages.

## Local case package

The local package may contain the complete VCF, pedigree, clinical notes, laboratory results, consent metadata and full analysis history. It remains under the originating institution's governance.

## Restricted review package

The restricted package contains the candidate variants, reviewed phenotype profile, inheritance hypotheses, selected supporting records and analysis provenance required by the panel. Direct identifiers are excluded when unnecessary. Each field should have an explicit access classification.

## Shareable variant knowledge object

The shareable object is intended for cross-case and cross-institutional reuse. A minimal model is shown in Table 4.

Table 4. Proposed fields for a shareable variant knowledge object.

| Category | Example fields |
|---|---|
| Variant identity | Reference assembly, normalized location and alleles, gene, transcript, HGVS expressions |
| Disease context | Disease identifier, inheritance mode, gene-disease validity and mechanism |
| Evidence | Evidence type, ACMG/AMP-style criterion, strength, source identifier, version and date |
| Review | Reviewing panel or jurisdiction, status, rationale, confidence and individual positions where permitted |
| Provenance | Analysis software, database versions, timestamps, creator and superseded record |
| Uncertainty | Conflicting evidence, rejected and pending criteria, missing data and re-evaluation triggers |
| Governance | Access tier, permitted use, consent or policy basis, attribution and license or data-use terms |

The model should support revision rather than overwriting. A new assessment can supersede an older assessment while retaining the earlier record and the reason for change.

# Hackathon outputs

The principal outputs developed or specified during MedHackathon Asia 2026 were:

1. an integrated architecture connecting local prioritization, phenotype review, restricted expert assessment and controlled knowledge sharing;
2. a set of VCF and phenotype input scenarios for prototype testing;
3. a practical target of reducing a large call set to approximately 10-20 review candidates;
4. an evidence matrix distinguishing automatable, semi-automated and expert-derived interpretation tasks;
5. a phenotype-review process with accepted, rejected and pending states;
6. a provisional comparison of regional resources and workflow constraints;
7. a per-case expert role model;
8. requirements for authentication, authorization, concurrency, consensus tracking and audit logging;
9. a definition of local, restricted and shareable information packages; and
10. an implementation roadmap connecting the existing ExpertBoard prototype to upstream data processing and future regional exchange.

These are design, requirements and early-prototype outputs. The hackathon did not establish clinical validity, diagnostic utility, sensitivity, specificity, inter-laboratory concordance or regulatory compliance.

# Discussion

The proposed network addresses two forms of fragmentation.

The first is analytical fragmentation. Different genome builds, transcript sets, annotation versions, frequency resources and filtering strategies can produce different candidate lists from the same underlying data. A harmonized exchange format cannot remove every local difference, but it can make each decision reproducible and comparable. The system should preserve filter reasons and lower-ranked candidates so that a variant excluded by one workflow can be examined by another.

The second is knowledge fragmentation. Expert reasoning is frequently retained in local reports, spreadsheets, presentations or meeting notes. A final label alone is difficult to reuse because it does not show which evidence was accepted, which was rejected and which assumptions were applied. Structured review objects can make this reasoning visible while preserving uncertainty.

Regional collaboration is particularly useful when evidence types are complementary. Population cohorts contribute ancestry-aware frequency and recurrence information. Clinical programs contribute phenotype, segregation, functional and longitudinal information. Connecting these resources can improve evidence visibility without requiring centralization of raw genomes.

The network must also manage disagreement constructively. Different classifications may result from different disease contexts, transcripts, evidence dates, local frequency resources or institutional policies. A side-by-side interface should expose these causes. A single merged classification would conceal useful information and could imply an authority that the project does not possess.

Automation can reduce repetitive work, but it can also make errors appear systematic and authoritative. The design therefore requires source-linked proposals, versioned algorithms and explicit human acceptance. Language models should be evaluated as retrieval and summarization aids, not as independent expert reviewers.

The proposed architecture is compatible with a federated future. An initial shared ExpertBoard instance may be practical for prototyping, while later deployments could retain separate national or institutional instances and exchange approved knowledge objects. GA4GH identity, access and data-use standards provide possible building blocks, but governance agreements and local accountability remain essential.

# Limitations

This report has several limitations.

First, it summarizes work in progress during a hackathon. Some requirements and workflow descriptions require confirmation after the final wrap-up and by representatives of the relevant institutions.

Second, no benchmark dataset was used to measure candidate retention or compare complete pipelines. The targets of 10-20 prioritized variants and approximately three variants for detailed board discussion are workflow goals, not validated clinical thresholds.

Third, the current ExpertBoard application is experimental. Authentication, reviewer actions, consensus finalization and complete audit behavior are not yet implemented or validated for clinical use.

Fourth, the initial workflow focuses mainly on small germline variants. Copy-number variants, structural variants, repeat expansions, mitochondrial variants, mosaicism and complex alleles require additional representation and evidence logic.

Fifth, ACMG/AMP-style criteria require gene-, disease- and mechanism-specific specifications. A generic system can organize evidence but cannot guarantee that a criterion or strength is appropriate.

Sixth, the provisional regional comparison is neither exhaustive nor an official statement of national policy. Resource availability, access procedures, guidelines and regulations change over time.

Seventh, de-identification does not eliminate re-identification risk in rare disease. Extremely rare variants and distinctive phenotype combinations may identify a person or family even without conventional identifiers.

# Future work

The next phase should proceed through measurable milestones.

1. **Finalize the common requirements.** Confirm the workflow, country descriptions, case lifecycle, roles and publication terminology with all contributors.
2. **Create a workflow inventory.** Document genome build, normalization, transcript selection, quality, population, inheritance, phenotype and disease-gene filters used by participating groups.
3. **Run a reproducible comparison.** Use synthetic, public or appropriately governed cases to compare candidate retention, ranking, runtime and explanations.
4. **Define the prioritization-to-review package.** Establish a machine-readable schema for variants, phenotypes, evidence proposals, provenance and filter history.
5. **Implement ExpertBoard review actions.** Add authentication, comments, individual positions, structured evidence editing, chair finalization, conflict detection and audit logging.
6. **Integrate phenotype review.** Support HPO-term provenance, accept/reject/pending states, missing-information prompts and PubCaseFinder-based disease or case suggestions.
7. **Implement panel comparison.** Display panel-specific evidence and conclusions side by side and identify the sources of concordance and discordance.
8. **Design governed knowledge exchange.** Define access tiers, de-identification review, attribution, correction, withdrawal, permitted use and licensing.
9. **Evaluate federated deployment.** Compare a shared pilot instance with separate institutional instances using standardized identity and knowledge exchange.
10. **Conduct a multicountry pilot.** Measure usability, turnaround time, candidate retention, reviewer agreement, evidence reuse and the effect of regional population resources.
11. **Develop sustainability and training.** Establish maintainers, contribution rules, documentation, example cases and training for reviewers and developers.

# Software and data availability

The ExpertBoard prototype is available at:

- https://github.com/PubCaseFinder/expertboard

The final BioHackrXiv manuscript source, figures and any reproducible benchmark materials should be maintained in a dedicated public repository with an explicit license. Patient-derived data must not be deposited unless sharing has been approved under the applicable consent, ethics and institutional requirements.

# Acknowledgements

We thank the organizers, local hosts, sponsors and participants of MedHackathon Asia 2026, held at Begonia Pavilion, Downtown East, Singapore, from 27-31 July 2026. We thank the members of the variant, phenotype and system-design workstreams for contributing requirements, examples and implementation discussions.

The descriptions of countries, institutions and resources in this report reflect informal hackathon discussions. They do not represent official national policy, clinical standards or the formal position of any organization.

# Author contributions

**Conceptualization:** TO BE COMPLETED.  
**Software:** TO BE COMPLETED.  
**Methodology and requirements analysis:** TO BE COMPLETED.  
**Variant workstream:** TO BE COMPLETED.  
**Phenotype workstream:** TO BE COMPLETED.  
**System-design workstream:** TO BE COMPLETED.  
**Visualization:** TO BE COMPLETED.  
**Writing - original draft:** TO BE COMPLETED.  
**Writing - review and editing:** All authors.

# Ethics statement

No new patient-level research analysis is reported in this manuscript. Any future use of patient-derived genomic or clinical information must follow applicable consent, ethics-review, privacy, security and institutional requirements.

# Competing interests

The authors declare no competing interests, subject to confirmation by all authors.

# References
