+++
title = Responsible Use of Machine Learning in Kilonova Seekers
date = 2025-12-15
+++

The use of artificial intelligence (AI) and machine learning (ML) has become a core component of many projects hosted on [Zooniverse](https://www.zooniverse.org) – using these techniques to amplify the impact of your work as volunteers, and scale to large datasets for large-scale scientific inquiry. AI now plays a pervasive role in society at large. Such techniques have been transformative, including for our work on [Kilonova Seekers](https://kilonova-seekers.org), but such tools must be used carefully to avoid amplifying biases or increasing inequality, and thus must be applied in a fair and responsible way. With this in mind, as we re-launch our project, we wanted to be mindful about being transparent about how, where, and when your contributions are used for machine learning. 

In this living document we also wanted to enumerate some of our core principles in the responsible use of AI and ML in our workflows, to ensure we make use of your contributions in ways that align with the open, ethical, and collaborative spirit of citizen science.

One of the core goals of Kilonova Seekers is to create a gold-standard set of human-labelled data for training machine learning classifiers for transient astronomy - to enable them to better deal with artifacts, and adapt to the ever-changing properties of the datastream. In new phases of the project, we aim to equip our ML classifiers with more powerful capabilities, including learning from volunteer contributions in real time, and being able to automatically identify the **type** of transient discovered.

The machine learning algorithms we use on the project are primarily classifiers - models that are trained to take images or other data as input, and predict which pre-defined class of object is seen in the data. We typically use [convolutional neural networks](https://en.wikipedia.org/wiki/Convolutional_neural_network), a classic architecture with a scale and compute footprint appropriate to the task.This is very different to generative AI or large language models such as ChatGPT which generate new data based on their training. These algorithms are not directly suitable for the science workloads we are trying to do with stochastic performance, are computationally intensive, and are much harder to understand the biases and alignment of - making it more difficult to ensure responsible use. 

# Your contributions are used in three primary ways by the Kilonova Seekers project:

**Batch ML:** Kilonova Seekers labels are used in “batch” machine learning - that is, we collect a large number of volunteer labels for a large dataset, combine predictions from individual volunteers into “gold-standard” labels that are more robust than those from individual volunteers, and use these to train large-scale machine learning classifiers. The current GOTO pipeline’s “real-bogus” classifier is a convolutional neural network, that uses a dataset that is comprised of ~10% (20,000 at the time of the most recent re-training) Kilonova Seekers gold-standard labels, with each label typically having 10-15 volunteers predictions averaged to create it. Batch ML retrainings happen typically every 6-12 months, when we’ve accumulated enough data to make a meaningful difference to performance, or when we observe performance degradation from “data drift” - where the properties of the incoming data can change over time. In the context of GOTO this could come from the optics changing over time, tweaks to the pipeline, and other subtle drifts that may not be seen by you, but affect our machine learning models.

**Online ML:** As part of the new version of Kilonova Seekers, we will be introducing “online” machine learning - models that update and learn in real time from volunteer inputs. Though less powerful than the batch ML classifiers, these methods have the advantage of being able to adapt rapidly to changes in the datastream, with the disadvantage of potentially having larger variance in performance. These techniques have the potential to act as an extra veto against artifacts, and enable more intelligent use of volunteer time in labelling the most unusual objects - known as “active learning”. This will be accompanied with “volunteer-in-loop” evaluation: using gold-standard subjects based on aggregation of labels (as in batch ML) to validate model performance in real time, to ensure these models are behaving as expected.

**Assessing the performance of humans at astronomical source classification:** Understanding biases in our training data is a crucial part of responsible model development. Although we look for “gold-standard” labels, those derived from human labelling will necessarily inherit bias from human classifiers, which in turn have a measurable impact on model performance. We conduct studies based purely on the Kilonova Seekers labels (both in an aggregate setting, and using individual sets of volunteer labels) to attempt to assess this, so that we can begin to quantify human-induced bias in our training sets, and mitigate it via better training and education, or more advanced aggregation.

At all steps, machine learning training and inference is performed locally, without sharing your labels with third parties.

What follows are some broader points that address our philosophy surrounding the use of ML as a project, and guide our volunteer interactions and project development.

## The role of volunteers alongside ML:  

Uploads to Kilonova Seekers are prioritised in two distinct ways to improve the project experience and make the most efficient use of labelling effort:
* Real-time discovery: to get a good sampling of high real-bogus scoring artifacts that we want to remove, and promote real transients that can be discovered by the volunteers, we upload a selection of candidates with real-bogus scores > 0.7. 
* De-biased/active learning: some of the data arriving on Kilonova Seekers is chosen to span the full range of real-bogus scores, to provide a representative span of artefacts and real transients with low scores. We do this by selecting candidates randomly with a weight proportional to 1/the number of objects with that real-bogus score.

_During Kilonova Seekers Gen 2:_ discovery alerts were triggered when a subject received at least 8 positive votes, and 80% agreement among all who classified. The final “retirement”, independent of whether real or bogus, occurred when 14 votes were reached. This was chosen to measure the probability of being real or not to ~10% accuracy under the assumption of a Binomial probability distribution.

For Kilonova Seekers Gen 3, these thresholds will need to be modified to account for the more complex multi-class workflows. We’ll update this document once these have been decided.

In the case of batch workflows where we combine answers from multiple participants, these will be merged using averaging, or more complex statistical modelling to estimate uncertainties. This will be done in a fair way (see Model bias and Fairness) that respects everyone’s contributions.

# Feedback and improvement:
As part of Kilonova Seekers we use “validation” subjects, subjects that we know the answer to already. In previous generations these did not behave differently to usual subjects, but in Gen 3 we will enable the “feedback” mechanism, which provides guidance on whether these were classified correctly. 
This is in order to a) provide feedback on classifications that enables volunteers to improve their classifying skills and gain confidence, and b) to enable us to estimate the performance of volunteers at classifying different types of sources for better statistical modelling. These will be updated semi-regularly to keep providing new training examples, and reviewed to ensure that their labelling is as accurate as possible.


## Model bias and fairness:
All machine learning models inherit biases from the training sets they are trained on - whether positive inductive biases that improve performance, or negative biases which decrease sensitivity to specific types of events or increase false positives. Whilst quantifying these biases is not always easy, we note below a few specific classes of bias that have been identified in earlier releases of Kilonova Seekers, for transparency:

* **Gen 1:** Cosmic rays were overrepresented in the “positive” label class, due to there being no easy way to identify these from the Gen 1 subjects. Models trained on this dataset inherited the bias. Issues with cosmics were fixed within the GOTO pipeline, and we added the subframes to the Zooniverse subjects to give volunteers better ability to identify these.
* **Gen 2:** False positive rates are higher around bright galaxies, especially those with poor subtractions. We suspect this is partially due to the reward of finding a supernova in a bright galaxy making volunteers more lenient with their classifications, but also due to the intrinsic difficulty of these subjects. We saw this bias in classifiers trained on this dataset. We are trialling improvements to subjects and workflows to mitigate this in Gen 3, and will update here.

As part of maintaining the quality of life of the project, we will attempt to mitigate these biases as soon as they are identified, and raise them once understood via the Talk boards and this document.

We plan to experiment with “weighting” of volunteer labels in final consensus, given our project relies on timely discovery of transients. In this approach, we will always include all labels - that is, everyone’s effort will count. Uncertainty estimation is a central motivation for our work, and having diverse labelling is crucial for this.  We are also investigating robust statistical modellinggenerative modelling of our dataset, which would remove the need for human-chosen weightings entirely.

## Crediting volunteers:
The primary method for crediting volunteers for discoveries in real time is through including them on reports we make as GOTO to the Transient Name Server (TNS). When the consensus threshold (see The role of volunteers alongside ML) is reached, we randomly select 5 of the 8 volunteers involved to be included on the report. This is done entirely randomly based on the information we have at the time the consensus threshold is reached. This has been in operation for the duration of the project - any changes to this strategy will be documented here.

Every volunteer who performs at least one classification will be included on our main “list of participants” page. This is dynamically generated from the list of user IDs who have classified on the project, with us polling the Zooniverse to retrieve an up to date credit name or display name for each participant. Names containing web URLs or email addresses are removed for privacy reasons, and the list is then sorted alphabetically.

Where a Kilonova Seekers discovery leads to a publication, we include volunteers who contributed through labelling it on the Zooniverse platform, if we as project members are involved in the publication. Given that we make our discoveries public immediately as a service to the time-domain community, we expect others may do their own studies on Kilonova Seekers objects - we cannot force the inclusion of volunteers in these situations, but hope that others will credit both the project and the nature of the discovery.

## Preserving educational value and discovery: 
Ensuring that Kilonova Seekers enables education alongside the citizen science aims remains a core part of our goals for the project. As the project evolves, we will continue to provide educational content as part of the project, via up-to-date field guides/project pages, and continued team/individual engagement with the project - as part of the broader educational mission of the Zooniverse.

Discovery is a core part of the Kilonova Seekers project, and we have taken steps to ensure that the genuine discoveries by the volunteers are preserved, even as ML classifiers grow in power and capability. Kilonova Seekers is directly connected to the live “GOTO Marshall”, where the team triage and report discoveries by GOTO. If Kilonova Seekers reaches consensus on a candidate prior to the GOTO team reporting a given object, a credit statement will be given as part of the report to the Transient Name Server. Volunteers are directly embedded in the live science pipeline, and so have the same possibility to make discoveries as the core GOTO team.

## Open science
We have a responsibility to ensure your work as volunteers is as useful as possible to the scientific community - both through making our data available in a responsible, maximally useful way that respects your contributions and privacy, and through documenting our project in an open-access way. We will ensure that manuscripts featuring Kilonova Seekers data and contributions are published in open-access journals, and pre-prints made available via arXiv. We commit to a public data release of Kilonova Seekers data at the conclusion of the project, so that others may benefit from our labels. In any public data releases, all information that could identify a volunteer personally will be pseudo-anonymised in a way that preserves privacy yet still allows useful aggregation of classifications (e.g. for building gold-standard datasets) - via cryptographic hashing and/or k-anonymity to preserve volunteer privacy. Public data releases will be accompanied with a peer-reviewed publication, documenting the structure, known biases, and provenance of the data.

## Model releases 
Any releases of fully-trained models based on Kilonova Seekers data will be accompanied with model cards, describing the dataset, architecture, use case, evaluation, and bias assessments for the model, and accompanied by a peer-reviewed paper as discussed above where appropriate. Whilst an open data release means we cannot control how others use trained models, the domains of models are carefully scoped to be specific to the task at hand, and thus the surface for model misuse is minimal. 

## Environmental impacts 
Machine learning is an intrinsically compute-intensive task, which with it brings the potential for significant greenhouse gas emissions. We ensure at all steps that the architectures and training are proportionate to the task at hand to minimise unnecessary emissions, by e.g. avoiding overly complex architectures such as Large Language Models, developing models of reduced size before scaling up where possible, and avoiding excessive hyperparameter optimisation. The computationale infrastructure and databases related to Kilonova Seekers are hosted in University-managed datacentres, powered by 100% renewable energy.

## Opting out 
We respect the preferences of our volunteers in how their data is used. If you do not want your annotations on Kilonova Seekers to be used in the training of machine learning classifiers, but still want to participate in the project, you can opt out by sending us your user ID -- **instructions to be added**. We will use this user ID to filter out your classifications for any machine learning tasks downstream, and ensure that your labels are not included in any final public data releases (where others may use them for machine learning). 

For those who wish to learn more about the importance of ethics in AI and ML in the broader context of society, we can highly recommend the free [“Ethics of AI” MOOC from University of Helsinki](https://ethics-of-ai.mooc.fi/). 

---

The Kilonova Seekers Team
15th December 2025
**version 0.9.0**
