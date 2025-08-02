In this study, we mined data from Reddit’s most popular psychedelic forums, creating an extensive dataset of written accounts of subjective experiences with psychedelic drugs (i.e., LSD, Psilocybin, DMT).
The present study utilises web-scraping techniques to collect data from Reddit’s most popular psychedelic forums, creating an extensive original dataset of open-source written psychedelic experiences.

Methods
Data Collection
The data for this study were collected and analysed using Python (Python Software Foundation, 2024). We web-scraped posts from the Reddit social media platform (https://www.reddit.com/), collecting users’ written descriptions of their psychedelic experiences, commonly known as ‘trip reports,’ where individuals narrate their personal psychedelic experiences.

We mined posts from 12 of the most popular subreddits related to classical psychedelics (see Appendix 1). To identify these subreddits, we searched for terms related to our three primary drugs of interest: LSD, Psilocybin, DMT, and the general term ‘psychedelics.’ Subreddits with over 100,000 members were selected for inclusion. Additionally, smaller subreddits with over 10,000 members, particularly those dedicated to sharing psychedelic experiences, such as ‘r/tripreports,’ were also included.

The data were downloaded on 20/05/2024 via Project Arctic Shift, an open-access pipeline for Reddit archival data. For each post, we extracted the subreddit name, author ID, timestamp (created_utc), title, post body (selftext), and associated flair tags.

Data Preprocessing
We implemented a multi-stage filtering pipeline combining LLM inference, structured annotation, and conservative exclusion rules. This process was designed to ensure that only first-person, retrospective trip reports with unambiguous temporal markers were retained for longitudinal analysis.

Initial Filtering Using Reddit Flair Tags
The initial dataset comprised 723,431 Reddit posts. Posts with irrelevant or generic flair tags (e.g., “General Question,” “Cultivation”) were excluded based on a manually reviewed list of 16 high-frequency non-report labels (Appendix 2), resulting in 623,367 posts. We further removed 10,385 duplicate text entries posted by the same user, resulting in 612,982 unique posts.

Trip Report Classification
To identify first-person trip reports and extract drug and timing information, we used Meta’s LLaMA-3 (8B parameters) deployed via the Replicate API. A custom zero-shot prompt was used to determine (a) whether the post described a psychedelic experience, (b) which drug(s) were involved, and (c) the time elapsed between the trip and the post. This model was chosen based on a balance of performance, transparency, and computational efficiency. Internal validation on 120 randomly sampled posts showed 93–97% alignment with human-coded ground truth across all three classification dimensions.

After running all posts through the model, we excluded non-trip reports (e.g., planning a future trip, general discussion about psychedelics; n = 505,254), posts without specific timing information (n = 34,681), those not specifying which drug(s) were used (n = 696), and those describing experiences with non-classical substances such as MDMA (n = 16,599). This resulted in 57,535 trip reports.

LLM-based Filtering
To estimate the specific date of the trip as accurately as possible, we implemented a conservative filtering pipeline for trip reports. We fine-tuned the OpenAI model to identify mentions about the timing of the psychedelic experience within our trip reports and then extracted the mentioned temporal annotations. Following this, we used the LLaMA-3 language model to normalise the temporal references. It converted each expression into a continuous variable representing the number of days relative to the trip (e.g., “yesterday” = −1), which allowed for the derivation of a continuous ‘days relative to trip’ variable.

Posts were retained only if they included an unambiguous calendar reference (e.g., “on March 8th,” “last Wednesday”) or a quantifiable relative timeframe (e.g., “3 days ago,” “yesterday”). Vague or approximate expressions (e.g., “a few months ago,” “last year,” “recently”) were excluded. To further enhance reliability, we excluded posts referencing psychedelic experiences that occurred more than six months prior to the post date (n = 8,642). After all filtering stages, the final dataset comprised 48,893 posts from 16,000 users (from an initial pool of 24,000).

BERTowid-based Filtering
We used the supervised BERTowid model on our Reddit dataset to output phenomenological, pharmacological, and demographic feature scores. It is a transformer-based model (i.e., BERT), previously trained on the Erowid dataset comprising 11,816 publicly available drug testimonials. BERTowid is a classifier specialised in online accounts of experiences with drugs, making it well-suited to the nature of our dataset and the task at hand. It can predict 28 dimensions of sentiment across each narrative—validated with psychiatrist annotations—as well as biochemical and demographic information.

To ensure that only genuine first-person reports of people describing their own experience and not someone else’s, we used the Second_Hand_Report scores generated by BERTowid. We reviewed the text entries and their associated scores. By inspecting the data, we set an arbitrary threshold of 0.02, above which participants were excluded, as manual inspection evidenced that these consistently described others’ experiences.

To ensure the entries were actually retrospective accounts rather than reports made during the acute state (it is known that psychedelics alter language widely during the psychedelic experience) or before the experience (e.g., planning their trip), we used the Retrospective_Summary scores generated by BERTowid. After reviewing the scores and their associated reports, we noted that even very low scores still represented valid retrospective accounts—likely due to our earlier filtering steps, which proved to be highly accurate.

After applying our conservative exclusion threshold to the Second_Hand_Report score, we removed posts scoring above 0.02. This excluded 315 entries (2.6%), yielding a final sample of 11,639 trip reports written by users with matching pre- and post-trip activity.

