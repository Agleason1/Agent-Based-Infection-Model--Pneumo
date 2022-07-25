We need:

-   Distribution of transmission rate by day (can use multiple
    distributions to model differences by day, or the averaged
    distribution across all days of infection).

    -   If we use an averaged distribution across all days of infection,
        we also need to know transmission rate trends per day: by how
        much does transmission increase for the first days of infection
        period until maximum value; by how much does it decrease during
        the last days of infection period until 0?

    -   CKK: I think the second idea is better. But we don't really have
        this data.

    -   CKK: as long as we know whether transmission peaks before or
        after symptomatic-ness, we could infer the rest here

        -   CKK + GGP's idea: here are the results of the model we've
            devised using some epi tricks. let's write a simple
            bacterial kinetics model with high uncertainty range that
            says the following:

            -   Incubation period of s. pneumoniae is 1 to 3 days (draw
                uniformly from that distribution):
                https://www.cdc.gov/vaccines/pubs/pinkbook/pneumo.html\#epidemiology

            -   transmission peaks 1 to 5 days after symptom onset
                (drawn uniformly from that period)

            -   the peak value of transmission is the highest value of
                the probability of transmission derived from whatever
                source you want to use with a range of p/m 10% of that
                value -- for instance, if the value you have is 0.25,
                then the range would be from \[0.25 -- 0.025, 0.25 +
                0.025\]. I think it would be worthwhile to run some
                small tests using the two probabilities of transmission
                from the two sources.

            -   transmission probability linearly increases from 0 at a
                day uniformly chosen from the window of \[day of
                infection (called day 0, if you'd like), day of symptom
                onset\]

            -   transmission probability linearly decreases such that it
                is \[1/3, 2/3\] of it's maximum by a day uniformly
                chosen in the window from \[3, 9\] days after the peak
                of transmission

            -   transmission probability exponentially decreases such
                that it is a random value picked uniformly from \[1/100,
                1/5\] of it's maximum by a day uniformly chosen in the
                window \[5, 25\] after the last chosen day

            -   the transmission probability can only go to 0 at this
                point for those individuals who are not carriers (see
                below for details). For those who are carriers, at this
                point, the probability of transmission stays flat at the
                value determined from above.

    -   CKK: we need to also use the superspreading distribution for ARI
        to pick the number of people that an agent will infect and then
        scale that distribution so that the mean is the R0 of s.
        pneumoniae (rather than being the R0 of just an arbitrary ARI)

        -   the idea here is that we need to pick how many people the
            agent will infect, and then the probability of transmission
            really just tells us *when* they will infect those people

            -   It's not a probability that it incorporated into the
                model. It is the approximate number of individuals the
                infected infects per day.

        -   so to come up with that superspreading distribution, let's
            use the data from this paper:
            <https://journals.plos.org/plosone/article/file?id=10.1371/journal.pone.0209039&type=printable>.
            What we want to look at is Fig. 4A which has a box plot for
            the number of contacts per children from an age group and
            Fig. 5A is a heatmap which has the average number of
            interactions between individuals in two age groups. Fit a
            neg binomial to that data (you can just extract the median
            and quartiles from Fig. 4A unless I think of a better idea
            and fit a neg binom that has those statistics) and then find
            the mean of that neg binom and scale the distribution by a
            factor of R0 of S. pneumoniae divided by the mean to get the
            actual distribution of the number of infectious contacts (to
            find our estimate for R0, look at the LSHTM thesis I sent
            you by the way but if you don't mind please also run R0 =
            1.1-1.4 because truthfully I think the LSHTM thesis is an
            overestimate. Moreover, note that there are two things that
            spread here -- the bacteria and the illness. You can be
            infected but not show symptoms.). However, we reserve the
            right to change this method if we think of something better
            :)

            -   Note the supplemental for this paper may provide better
                data:
                <https://s3-eu-west-1.amazonaws.com/pstorage-plos-3567654/13897421/pone.0209039.s001.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAI5TSI4CIU73QDJOQ/20220721/eu-west-1/s3/aws4_request&X-Amz-Date=20220721T093047Z&X-Amz-Expires=10&X-Amz-SignedHeaders=host&X-Amz-Signature=024883c003c569afe454a0e8173a981a6e4286ec0e0c8b7d8d333cf759e06da5>

        -   if you want to get advanced here (and this will be
            worthwhile doing in the future), we can also include
            transmission rates that change with seasonality:
            <https://www.nature.com/articles/srep11344.pdf>. Basically,
            you can scale the number of contacted people by the increase
            in the transmission probability that occurs with seasonality
            changes

Now is when things start to get a bit more tricky and we need to play
some epidemiology tricks.

-   Distribution of death probability by day (can use multiple
    distributions to model differences by day, or the averaged
    distribution across all days of infection).

    -   If we use an averaged distribution across all days of infection,
        we also need to know death probability trends per day: by how
        much does death probability increase for the first days of
        infection period until maximum value; by how much does it
        decrease during the last days of infection period until 0?

    -   CKK: I think this is a bit overkill-- I think what we should do
        is find the number of days it takes until someone dies (i.e.,
        the distribution of days till death), and pick a random day from
        that distribution for the agent *if* they die

    -   here's the best data we can get for how long it will take a
        child to die:
        <https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3167921/> (see
        Fig. 3)

    -   we need the overall probability of death as well. This depends
        on the serotype (more on this below), but the data is here:
        <https://www.mdpi.com/2076-2607/9/11/2286> (in spain, so not the
        biggest fan of this and actively searching out potential other
        papers with better data). Scratch that -- please use this:
        "Association of Serotype with Risk of Death Due to Pneumococcal
        Pneumonia: A Meta-Analysis"



-   Distribution of duration of infection for the population

    -   CKK: not quite. s. pnemoniae has what's known as long-term
        carriage where you can hold the bacteria for a long time.
        Children and adults can be carriers, but rates of carriage among
        children are much higher than those among adults, so this is the
        motivation for why we only model children. In fact, we assume
        that any carriage among adults is not going to trigger a
        secondary infection and is likely because the adult is actually
        *sick* with the disease (i'll discuss how to model this as the
        next step). However, carriage rates vary widely among different
        types of serotypes.

    -   This paper gives rates of carriage in table 1 (asymptomatic
        children):
        <https://journals.plos.org/plosone/article/file?id=10.1371/journal.pone.0246522&type=printable>.
        Assume that vaccinated children with PCV7 cannot have any of the
        PCV7 serotypes. This paper gives the best way of estimating
        which serotype you may have:
        <https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3381638/pdf/cis371.pdf>.
        Look at Table 1 and Fig. 2. Also look at this paper for insights
        into which serotypes you can get once vaccinated vs not
        vaccinated: "Streptococcus pneumoniae outbreaks and implications
        for transmission and control: a systematic review"

    -   for those children who are carriers, they maintain some
        probability of transmission (as mentioned above), and now this
        counts as an actual probability of transmission.


-   Distribution of reinfection probability for the population

    -   CKK: YEP! Most important piece of data that we still need to
        acquire. The serotypes afford cross-protection. GGP is working
        on getting this data. I don't have a good solution here. For
        now, assume perfect immunity for some time less than 3 years,
        and then assume no remaining immunity unless against the exact
        serotype that you were initially infected with.


-   Distribution of vaccine efficacy for the population

    -   CKK: eh, we can kind of make this up based on clinical data and
        use huge uncertainty intervals because the honest truth is that
        no one really knows how good these vaccines are...

    -   Use this as a broad estimate:
        <https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4985133/>.

    -   Later: assume that vaccines are effective as described in the
        above paper for three years and then efficacy decreases linearly
        so that by the end of three more years you have no immunity.


-   Prevalence of disease that leads to outbreak (may consider sampling
    from some distribution of prevalence at the district level to assign
    different values by district)

    -   Do we want to use NFHS data to model those most probable to have
        infection at start? If so, need more data to model this.

    -   CKK: let's use Farooqi et al. and Wahl et al. They provide
        incidence rates by state. Data from Farooqi et al. is in the
        variables spreadsheet I sent you. This is Wahl:
        [https://www.thelancet.com/action/showPdf?pii=S2214-109X%2819%2930081-6](https://www.thelancet.com/action/showPdf?pii=S2214-109X(19)30081-6)
        but I believe Farooqi et al. will be much more useful. The other
        way to do this is to try to back-compute the prevalence by
        seeing what the number of deaths are by state from the Wahl
        paper, but that sounds like a painful amount of math and model
        testing\...


-   Are we modeling individuals ages 1-5? We should update the contact
    matrix accordingly, and find the right data for it

    -   We can consider incorporating multiple other contact matrices
        using NFHS data for attributes besides age, and using the
        contact matrices to create a more comprehensive interaction
        matrix for the population

    -   CKK: No, we are modeling for ages \<13 (birth to age 13): we
        should use the school contact matrix and the household contact
        matrices to model these two types of behavior. For young ages,
        at \<3, the child only experiences the household contact matrix,
        and after that the child experiences *both* transmission
        matrices because infection can happen within a household

    -   Contact matrices are found with this paper:
        <https://www.nature.com/articles/s41467-020-20544-y.pdf> and
        <https://github.com/mobs-lab/mixing-patterns/tree/main/data/contact_matrices>.
        This is an utterly fantastic paper and exceptionally
        well-written too. It's incredible the data they provide! Use the
        state-specific contact matrices, please. Please also only use
        age as the contact matrix factor. Maybe gender eventually, but
        for now just age and let's keep it simple.

    -   CKK: please use the NFHS data to representatively sample from
        the ages and the population characteristics present in India.
        Please see the code I've sent you on how to do this.


-   Vaccinations:

    -   What proportion of population gets vaccinated when vaccinations
        are available? (Maybe we don\'t need this data, we might instead
        show how effective vaccinations are by frequency of
        administration)

    -   CKK: we have this information in the variables spreadsheet I
        sent you. That provides the fraction of people who are
        vaccinated by age, gender, education, wealth, and state of
        parent/head of household. Please representatively sample from
        the joint distributions for those variables to determine their
        prevalence in the population and then actual realistic
        probabilities of vaccination.

    -   Can total vaccination by day be modeled by exponential decay or
        some other function? (we currently estimate using weak
        exponential decay)

    -   CKK: confused at what this means and why we need this data.

    -   Do we want to model booster vaccinations? If so, how much does
        booster add to total vaccine efficacy?

    -   CKK: not relevant at this point. May be interesting to argue for
        a booster later and consider waning immunity, but not the focus
        at this time.

From here: all subject to change. We are still figuring out how to do
this. In the meantime, please read the following papers:

<https://nyaspubs.onlinelibrary.wiley.com/doi/epdf/10.1111/nyas.14571>

-   <https://www.nature.com/articles/s41586-020-2238-4>Antibiotics: (I
    am not entirely confident that below encompasses all data we need as
    we have not tried implementing this yet)

    -   What proportion of infected are treated with antibiotics?

    -   CKK: assume all.

    -   What is the probability of administration of each variant of
        antibiotic?

    -   CKK: I have this question myself. Assume 80% probability that
        you get penicillin first, 15% you get erythromycin, and 5% you
        get chloramphenicol. However, this is very iffy. The question is
        do we really need to model this? Need to think more about
        this....

    -   What is the probability that antibiotic will be effective (for
        each variant)?

    -   CKK: we have some data on this: take a look here
        <https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3510899/pdf/IJMR-136-495.pdf>

    -   I also sent you a paper which provided evidence about this:
        "Streptococcus pneumoniae outbreaks and implications for
        transmission and control: a systematic review"

    -   How does effective antibiotic treatment affect transmission rate
        and death probability by day of administration?

    -   CKK: abx administration means you will be cured in two days --
        but does that mean you will still not transmit? I don't know....
        We need to look to clinical studies to determine how long it
        takes an abx to kill the bacteria. We need to figure out more
        about this.

    -   If antibiotic fails, what is the probability that the infected
        individual receives a different antibiotic?

    -   CKK: fantastic question. They will receive another antibiotic
        for sure, the question is what. 50%

    -   By how much does effective antibiotic shorten infection period
        (preferably distribution of values by antibiotic variant)?

    -   CKK: said above

Consider these papers which may be interesting (I am reading them too
for further ideas):

https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6838571/pdf/tpmd180930.pdf

https://www.pnas.org/doi/pdf/10.1073/pnas.1718712115

<https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3566073/pdf/pone.0056079.pdf>
