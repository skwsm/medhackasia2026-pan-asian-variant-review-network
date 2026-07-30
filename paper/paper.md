---
title: "Toward Collaborative Rare Disease Variant Review Across Asia: A MedHackathon Asia 2026 Framework for Variant Prioritization, Expert Interpretation, and Knowledge Sharing"
title_short: "MedHackathon Asia 2026: collaborative variant review"
tags:
  - rare disease
  - variant interpretation
  - genomic medicine
  - expert panel
  - Asia
authors:
  - name: Toyofumi Fujiwara
    orcid: 
    affiliation: 1
  - name: George Devasia
    orcid: 
    affiliation: 2
  - name: Rutharra Ghayadthri
    orcid: 
    affiliation: 3
  - name: Vasanthan Jayakumar
    orcid: 
    affiliation: 4
  - name: Yuko Kitano 
    orcid: 
    affiliation: 5
  - name: Yosuke Kawai
    orcid: 
    affiliation: 1
  - name: Shuichi Kawashima
    orcid:
    affiliation: 1
  - name: Francis A. Tablizo
    orcid: 
    affiliation: 6
  - name: Shoichiro Takahashi
    orcid: 
    affiliation: 8
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
  - name: Trinet Corporation, JAPAN
    index: 8
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

Whole-genome and whole-exome sequencing can produce tens of thousands of variants for a single individual, while only a small number are plausible candidates for a rare or undiagnosed disease. The practical bottleneck is therefore not limited to variant calling. It also includes evidence-aware prioritization, integration of phenotype and inheritance information, multidisciplinary expert review, and reuse of prior interpretations. These challenges are amplified in Asia, where genomic resources, clinical workflows, population-frequency datasets, and governance requirements vary among countries and institutions.

During MedHackathon Asia 2026, Team 3 developed a framework for collaborative rare disease variant review across Asia. The proposed architecture connects three stages: (1) local annotation and prioritization of variants from WGS/WES data, reducing a large VCF to approximately 10-20 candidates; (2) structured review of the highest-priority candidates in a restricted expert-board workspace using ACMG/AMP-style evidence; and (3) controlled sharing of de-identified, reusable variant-level evidence and expert-reviewed annotations. Patient-identifiable data remain within the originating institution, while shareable knowledge is separated into explicitly governed information objects.

The framework builds on ExpertBoard, an experimental prototype that supports case-specific review rooms, multidisciplinary roles, evidence and decision tracking, and human-governed final interpretation. Hackathon discussions comparing phenotype-driven clinical workflows and population-based variant evaluation highlighted their complementarity: regional allele-frequency observations can inform clinical review, while phenotype-supported expert assessments can improve the interpretation of variants previously classified as uncertain.

This report describes the motivation, architecture, information boundaries, prototype scope, and implementation roadmap. The current work is a design and early-prototype report; no clinical performance or diagnostic utility has yet been established. Future work will focus on harmonized prioritization profiles, interoperable evidence models, multicountry pilot panels, side-by-side comparison of country-specific reviews, and governance for sustainable regional knowledge sharing.

# Introduction

Rare and undiagnosed disease analysis commonly begins with whole-genome sequencing (WGS) or whole-exome sequencing (WES), followed by variant calling and the generation of a Variant Call Format (VCF) file. A single case may contain tens of thousands of small variants before detailed filtering. Even after basic technical and frequency filters, many candidate variants may remain, particularly when phenotype data are incomplete, inheritance is uncertain, or existing databases contain limited evidence for the patient's ancestry.

Variant interpretation is supported by resources such as the Genome Aggregation Database (gnomAD), ClinVar, ClinGen, the Ensembl Variant Effect Predictor (VEP), the Human Phenotype Ontology (HPO), and Online Mendelian Inheritance in Man (OMIM) [@Karczewski:2020; @Landrum:2025; @Rehm:2015; @McLaren:2016; @Kohler:2021; @Amberger:2015]. The ACMG/AMP framework provides a widely used structure for combining evidence toward variant classification, and subsequent ClinGen work has refined the interpretation and weighting of individual criteria [@Richards:2015; @Tavtigian:2018]. Nevertheless, interpretation remains dependent on disease context, transcript and variant representation, phenotype quality, segregation, functional evidence, expert judgment, and the population resources available to the reviewing laboratory.

Asian populations remain unevenly represented in commonly used genomic resources. Studies of ancestrally diverse Asian genomes have shown both ancestry-specific clinically relevant variants and variants whose interpretation may change when additional regional observations become available [@Chan:2022]. MedHackathon Asia was established to strengthen practical collaboration in genomic and health data across the region. The 2026 meeting emphasized interoperable workflows, population-aware interpretation, federated and secure research environments, and responsible governance [@MedHackathonAsia:2026; @MedHackathonCommunity:2026].

Against this background, Team 3 discussed a regional platform that links variant prioritization, expert review, and reusable knowledge sharing. The working concept was initially referred to as the *Pan-Asian Variant Review Network*. The intended system is not a centralized authority that replaces local clinical practice. Rather, it is a collaborative research infrastructure through which panels in different countries can review the same variant, preserve their own reasoning and uncertainty, and display their assessments side by side.

# Objectives and scope

The long-term objective is to create a workflow that can accept genomic and clinical inputs, produce a manageable candidate list, support multidisciplinary expert review, generate a case-oriented report, and retain reusable variant knowledge.

The proposed scope is defined by five principles:

1. **Post-calling prioritization rather than replacement of variant calling.** The framework begins with a VCF or equivalent variant representation produced by an upstream WGS/WES pipeline.
2. **Human-governed interpretation.** Automated methods may annotate, rank, and suggest evidence categories, but they do not make the final clinical interpretation.
3. **Separation of patient data from reusable knowledge.** Patient-identifiable or potentially re-identifiable information remains within the originating institution or an approved restricted environment.
4. **Preservation of local perspectives.** Country- or institution-specific expert panels can record distinct assessments. The platform should visualize agreement and disagreement without requiring a single regional consensus.
5. **Progressive implementation.** The immediate work focuses on a practical variant-prioritization prototype and its connection to the existing ExpertBoard application. Full EHR integration, automated phenotype extraction, multicountry federation, and validated reporting are future stages.

# MedHackathon Asia 2026 discussions

## Comparison of participating workflows

Participants discussed current approaches used in Japanese clinical rare disease analysis and Singaporean research and population-genomics settings. The comparison was not intended to define official national workflows. Instead, it identified complementary resources and recurring implementation differences.

The Japanese workflow described by participants was primarily clinical and phenotype-driven. Local variant databases, patient phenotype information, disease knowledge, and expert-board discussion were important in reducing and interpreting candidate variants. The Singaporean discussion emphasized standardized and versioned analysis pipelines, VEP-based annotation, local and regional allele-frequency resources, and population-scale cohorts such as SG10K and SG100K. Research cases without detailed phenotype information and clinical cases with phenotype-rich records therefore offered different but complementary evidence.

The group identified several reasons why the same starting data could lead to different shortlists:

- differences in genome builds, transcript sets, variant normalization, and annotation versions;
- different population and local-frequency databases;
- different allele-frequency thresholds and inheritance filters;
- different handling of splice, loss-of-function, structural, and low-quality variants;
- incomplete or differently encoded phenotype information;
- different disease-gene resources and computational prediction tools; and
- institutional differences in review practice and evidentiary thresholds.

## Agreed hackathon scope

The group concluded that implementing a fully validated end-to-end clinical system during a single hackathon was unrealistic. The near-term focus was therefore narrowed to the variant analysis and prioritization stage, while preserving clear interfaces to phenotype extraction and expert review.

A sample VCF was made available for comparing candidate workflows. The practical next steps were defined as:

1. document the annotation and filtering steps used by participating groups;
2. identify a minimal common annotation profile;
3. compare the resulting candidate variants;
4. define a structured package that can be transferred from local prioritization into ExpertBoard; and
5. specify which reviewed information can later be shared regionally.

# Proposed system architecture

Figure 1 summarizes the proposed three-layer architecture. The separation between local, restricted, and shareable environments is a core design requirement rather than only a deployment preference.

![Figure 1. Proposed workflow from local WGS/WES and phenotype processing to restricted expert review and controlled release of reviewed results. The architecture separates patient-level processing from internet-accessible expert review and public or controlled knowledge sharing.](figures/workflow_architecture.png)

## Stage 1: local variant annotation and prioritization

The first stage operates within an on-premises environment, trusted research environment, or otherwise approved local infrastructure. Raw reads and upstream variant calling are outside the immediate scope. The input is a VCF or equivalent set of variant calls, normally containing approximately \(10^4\)-\(10^5\) variants for a WGS/WES case depending on scope and preprocessing.

Candidate variants are annotated and prioritized using a combination of:

- genome build, normalized coordinates, reference and alternate alleles;
- affected gene, transcript, molecular consequence, and predicted loss-of-function status;
- ClinVar and ClinGen evidence;
- global, ancestry-specific, local, and regional allele frequencies;
- disease-gene associations;
- computational predictions;
- inheritance model and family structure;
- variant quality and sequencing context;
- phenotype relevance based on HPO or related structured terms;
- literature and functional evidence; and
- institutional rules or disease-specific criteria.

Clinical notes may be manually encoded or processed through a phenotype-extraction component to generate candidate HPO and disease terms. Automated phenotype extraction must retain the original text span, provenance, confidence, and reviewer correction because incorrect phenotype abstraction can substantially change ranking.

The intended output is a structured shortlist of approximately 10-20 candidates. The number is a workflow target, not a fixed clinical rule. The shortlist should preserve excluded and lower-ranked variants, filter reasons, software and database versions, and sufficient provenance to reproduce the prioritization.

## Stage 2: restricted expert-board review

The prioritized candidates are transferred to an authenticated expert-review environment. The highest-priority variants, often approximately three for detailed discussion, are reviewed in the context of the case.

Evidence is organized using ACMG/AMP-style categories, including population evidence, computational evidence, phenotype specificity, segregation, functional evidence, de novo occurrence, allelic data, and prior reports [@Richards:2015]. The system may propose candidate criteria or evidence strengths, but each proposal must remain reviewable. Experts can accept, reject, modify, or leave criteria pending.

The review workspace should capture:

- the evidence item and its source;
- the criterion and proposed evidence strength;
- the reviewer who proposed or evaluated it;
- accepted, rejected, pending, or not-applicable status;
- written reasoning and uncertainty;
- conflicting interpretations;
- requests for additional tests or family studies;
- the board's current assessment and confidence;
- timestamps, versions, and audit history; and
- recommended next steps.

A multidisciplinary board may include a case chair, clinical reviewer, bioinformatician, laboratory scientist, genetic counselor, case coordinator, and external consultant. Final decisions remain human-governed. The system should distinguish between an individual reviewer's position, a panel-level summary, and any formal classification issued by an accredited clinical service.

## Stage 3: regional knowledge sharing

The third stage converts appropriate portions of the review into reusable regional knowledge. Two broad information classes were identified.

The first class consists of generally reusable evidence that is independent of a particular patient, including:

- regional or local frequency observations at an approved aggregation level;
- literature and functional evidence;
- gene-disease relevance;
- transcript and molecular consequence information;
- prior expert comments that can be de-identified;
- evidence-code assessments and their provenance; and
- versioned links to public knowledge resources.

The second class consists of expert-reviewed variant annotations derived from selected cases. Depending on consent, policy, and re-identification risk, this may include the panel assessment, accepted and rejected criteria, unresolved uncertainty, and recommended evidence needed for re-evaluation. Patient-specific phenotype combinations, family structures, dates, and free text should not be released unless explicitly permitted and adequately governed.

Shared records should include the reviewing panel or jurisdiction, date, applicable guideline version, evidence sources, and review status. This makes it possible to present Japanese, Singaporean, Indian, and other panel assessments side by side. Differences should be treated as data requiring explanation, not automatically resolved into a single answer.

# ExpertBoard prototype

ExpertBoard is an experimental standalone prototype for AI-assisted complex case review [@ExpertBoard:2026]. It provides reusable expert boards organized by gene, disease, or clinical domain and case-specific review rooms in which multidisciplinary experts can evaluate evidence.

The current minimum viable product includes:

- expert-board listing and demo-board creation;
- case-review rooms under a fixed board;
- phenotype, variant, hypothesis, evidence, history, and briefing views;
- core and optional support roles;
- database-backed cases, membership, comments, decisions, and audit logs; and
- a placeholder for integration with the PubCaseFinder API.

For this project, ExpertBoard provides the human-review layer between local prioritization and shareable regional knowledge. Future development should add structured ACMG/AMP evidence objects, comparison of panel-specific assessments, explicit uncertainty states, configurable access controls, report templates, and export of de-identified variant-level knowledge.

Artificial intelligence may be used for evidence retrieval, literature summarization, phenotype-term suggestions, or candidate criterion suggestions. However, generated content must be linked to source evidence and must not be treated as a final interpretation without expert verification.

# Proposed information model

Interoperability requires more than agreement on a user interface. The project proposes three related information packages.

## Local case package

The local case package can contain identifiable or sensitive data and therefore remains within the responsible institution. It may include the full VCF, pedigree, clinical notes, phenotype timeline, laboratory results, consent metadata, and complete analysis history.

## Restricted review package

The restricted review package contains only the information required by authorized experts. It includes the candidate shortlist, relevant phenotype terms, inheritance hypotheses, selected supporting documents, analysis provenance, and structured evidence objects. Direct identifiers should be removed wherever they are unnecessary for review.

## Shareable variant knowledge object

The shareable object is designed for reuse across cases and institutions. A minimal record should include:

| Category | Proposed fields |
|---|---|
| Variant identity | Reference assembly, normalized location and alleles, gene, transcript, HGVS expressions |
| Disease context | Disease identifier, inheritance mode, gene-disease validity |
| Evidence | Evidence type, criterion, strength, source identifier, source version, date |
| Review | Panel or jurisdiction, reviewer role where permitted, status, rationale, confidence |
| Provenance | Software and database versions, creation time, superseded record, audit reference |
| Governance | Access level, permitted uses, license or data-use conditions |
| Uncertainty | Conflicting evidence, unresolved questions, conditions for re-evaluation |

A shared record should not be interpreted without its disease context, transcript, guideline version, and provenance. Variant-level statements can otherwise be misleading when moved between diseases, inheritance modes, or technical representations.

# Regional feedback model

Figure 2 illustrates a possible feedback loop between population-based variant resources and phenotype-driven expert review. The scenario is conceptual: it does not report an actual reclassification produced during the hackathon.

![Figure 2. Conceptual regional feedback loop. Population-based observations can supply regional frequency and recurrence evidence to a phenotype-driven expert board, while reviewed conclusions can improve a shared variant knowledge resource. The displayed reclassification is illustrative rather than a validated result.](figures/regional_feedback_loop.png)

A variant observed in a population cohort may remain a variant of uncertain significance when phenotype and family evidence are unavailable. Conversely, a clinical case may contain strong phenotype and segregation information but lack sufficient regional frequency context. Linking the two perspectives can identify evidence gaps, prioritize functional studies, and support future re-evaluation.

The platform should not directly import a classification from one panel as a decision for another patient. Instead, it should expose the underlying evidence, context, and provenance so that each panel can perform an independent assessment.

# Governance, privacy, and responsible use

Cross-border genomic collaboration is constrained by differences in privacy law, consent, institutional policy, data-access processes, and technical infrastructure [@MedHackathonCommunity:2026]. The proposed system therefore follows a knowledge-sharing model rather than assuming that patient-level genomes will be centralized.

Key governance requirements include:

- data minimization at each transfer boundary;
- explicit classification of local, restricted, controlled, and public information;
- role-based access and auditable actions;
- retention of consent and permitted-use metadata;
- institutional and country-specific review before data export;
- procedures for correction, withdrawal, and superseding interpretations;
- policies for conflicts of interest and expert-panel membership;
- clear distinction between research discussion and accredited clinical reporting; and
- security review for any internet-accessible deployment.

De-identification is not an absolute guarantee of anonymity for rare disease cases. Unusual phenotype combinations, family structures, geographic information, and extremely rare variants may permit re-identification. Consequently, case-derived information should undergo governance review even when conventional direct identifiers have been removed.

# Hackathon outputs

The work completed or initiated during MedHackathon Asia 2026 included:

1. a shared problem statement linking variant prioritization, expert review, and regional knowledge reuse;
2. a three-layer architecture separating local analysis, restricted review, and controlled release;
3. identification of complementary Japanese and Singaporean use cases;
4. definition of the immediate implementation scope around post-calling annotation and prioritization;
5. an initial ExpertBoard prototype and role model;
6. a draft distinction between reusable evidence and case-derived expert-reviewed annotations;
7. a sample VCF and plan for cross-workflow comparison; and
8. a roadmap toward multicountry expert panels and side-by-side display of their assessments.

These are design and prototype outputs. The hackathon did not establish diagnostic performance, inter-laboratory concordance, regulatory compliance, or clinical utility.

# Discussion

The proposed framework addresses two connected bottlenecks in rare disease genomics. First, institutions use heterogeneous pipelines and resources to reduce large VCFs to a tractable candidate set. Second, the reasoning generated during expert review is often stored in local reports, email, slide decks, or unstructured meeting notes and is therefore difficult to reuse.

A regional platform can add value without centralizing all genomic data. Population cohorts can provide ancestry-aware frequency observations, clinical programs can contribute phenotype-rich evidence, and expert panels can expose their reasoning and uncertainty. Structured provenance allows reviewers to understand why assessments differ. A side-by-side interface can show, for example, that one panel applied a population criterion using a local database while another considered phenotype specificity or segregation evidence. This is more informative than displaying only final labels.

The architecture also supports future learning systems. A corpus of reviewed evidence decisions could be used to evaluate evidence-retrieval methods, identify common sources of disagreement, and assist experts with prior similar reviews. Such use requires careful separation between training data and evaluation cases, transparent model behavior, and continuous human oversight.

The project should avoid presenting itself as a single Asian clinical authority. National and institutional autonomy, local regulation, and differences in available evidence must remain visible. The network's academic role is to make reasoning more interoperable and reusable while helping participants learn from each other.

# Limitations

This report has several limitations.

First, the architecture has not yet been evaluated using a defined benchmark set. The proposed reduction to 10-20 candidates and detailed review of approximately three variants are workflow targets rather than validated thresholds.

Second, the current ExpertBoard implementation is an experimental prototype and is not a certified medical device or accredited clinical reporting system. Its security, usability, auditability, and decision-support behavior require formal evaluation.

Third, the project has not yet established a common representation for all relevant variant types. Small variants are the initial focus, while copy-number variants, structural variants, repeat expansions, mitochondrial variants, mosaicism, and complex alleles may require additional workflows.

Fourth, ACMG/AMP criteria require disease-, gene-, and mechanism-specific refinement. A generic implementation can organize evidence but cannot guarantee a correct classification.

Fifth, no multicountry panel exercise has yet measured agreement, disagreement, review time, or the incremental value of regional frequency data. Legal, ethical, consent, language, sustainability, and attribution models also remain to be defined.

# Future work

The next phase will proceed through incremental, testable milestones.

1. **Workflow inventory and common profile.** Document annotation, normalization, transcript selection, quality, frequency, inheritance, phenotype, and disease-gene filters used by participating institutions.
2. **Benchmark comparison.** Run synthetic, public, or appropriately de-identified cases through participating workflows and compare candidate retention, rank, runtime, and explanations.
3. **Prioritization-to-review interface.** Define a machine-readable package for transferring candidate variants and provenance into ExpertBoard.
4. **Structured evidence model.** Represent evidence items, ACMG/AMP-style criteria, source versions, reviewer actions, uncertainty, and superseding assessments.
5. **Panel comparison interface.** Display country- and institution-specific reviews side by side, including shared and differing evidence, without forcing consensus.
6. **Federated deployment model.** Evaluate separate national or institutional ExpertBoard instances with a controlled exchange of variant knowledge objects and aggregated queries.
7. **Governance framework.** Define access tiers, consent requirements, data-use terms, attribution, withdrawal, correction, and responsibility for downstream reuse.
8. **Prospective pilot.** Conduct a multicountry pilot with expert panels from Japan, Singapore, India, and other interested locations, followed by evaluation of usability, concordance, turnaround time, and evidence reuse.
9. **Sustainability and community building.** Establish maintainership, documentation, training materials, contribution processes, and a neutral academic governance structure.

# Software and data availability

The ExpertBoard prototype is available at:

- https://github.com/PubCaseFinder/expertboard

The manuscript source, figures, and the exact location of any hackathon comparison datasets should be placed in a dedicated publication repository before submission. Patient-derived data must not be included unless sharing is explicitly approved.

# Acknowledgements

We thank the organizers, hosts, sponsors, and participants of MedHackathon Asia 2026, held in Singapore from 27-31 July 2026. We particularly thank the members of Team 3: Variant Team for discussions on rare disease workflows, population resources, expert review, and regional collaboration.

The descriptions of country and institutional workflows in this report reflect hackathon discussions and do not represent the official position or standard practice of any country, institution, clinical laboratory, or national genome initiative.

# Author contributions

**Conceptualization:** TO BE COMPLETED.  
**Software:** TO BE COMPLETED.  
**Methodology:** TO BE COMPLETED.  
**Investigation and discussion:** TO BE COMPLETED.  
**Visualization:** TO BE COMPLETED.  
**Writing - original draft:** TO BE COMPLETED.  
**Writing - review and editing:** All authors.

# Competing interests

The authors declare no competing interests, subject to confirmation by all authors.

# References
