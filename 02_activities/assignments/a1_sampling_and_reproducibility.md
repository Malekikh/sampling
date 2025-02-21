
# ASSIGNMENT: Sampling and Reproducibility in Python 

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

# portion 1
Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.
# portion 2
Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?
# portion 3
Modify the number of repetitions in the simulation to 100 (from the original 1000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.
# portion 4
Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: YOUR NAME

```
Please write your explanation here...

## portion 1
This simulation uses a multi stage sampling procedure. 

First starting with random sampling for infection assignment - A random sample of indices from the DataFrame is selected.
sample size here for infected people is 10% of the 1000 ppl. In this case, every individual has an equal chance of being selected (sampling without replacement).

  infected_indices = np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False)
  ppl.loc[infected_indices, 'infected'] = True


Second part is the tacing among the infected people. For each infected individual, a random number is drawn from a uniform distribution between 0 and 1.  If the random number is less than 0.20 (TRACE_SUCCESS), that individual is marked as traced.

    ppl.loc[ppl['infected'], 'traced'] = np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS


third part for second tracing, it counts the number of traced individuals per event type. Identify event types (e.g., 'wedding' or 'brunch') that meet or exceed the threshold (at least 2 traced cases). For these events, any infected individual is then marked as traced (secondary tracing). based on my search, this portion is called condition based sampling applied after observing the data. 

  event_trace_counts = ppl[ppl['traced'] == True]['event'].value_counts()
  events_traced = event_trace_counts[event_trace_counts >= SECONDARY_TRACE_THRESHOLD].index
  ppl.loc[ppl['event'].isin(events_traced) & ppl['infected'], 'traced'] = True

sample frame: indivisuals attending both events.
  events = ['wedding'] * 200 + ['brunch'] * 800
  ppl = pd.DataFrame({
      'event': events,
      'infected': False,
      'traced': np.nan  # Initially setting traced status as NaN
  })

sample size: 1000 which is total of 800 + 200 people attending events
results = [simulate_event(m) for m in range(1000)]

# portion 2 

Yes they both produce same graph results using same methodology and procedure. The only difference would be that blog is using 50,000 iterations as opposed to our code that is iterating 1000 only. But at the end we are getting similar outcomes. 

# portion 3
When reducing the number of repetitions to 100 instead of 1000, we observe greater variation in results. Reproducibility decreases because the lower number of trials increases randomness in each run.

# portion 4
we need to specify a random seed so everytime we run the code, it produces similar output. Right before the operation to ensure reproducibility as below:
np.random.seed(123)
results = [simulate_event(m) for m in range(1000)]


## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `23:59 - 16/02/2025`
* The branch name for your repo should be: `assignment-1`
* What to submit for this assignment:
    * This markdown file (a1_sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `assignment-1`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via the help channel in Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
