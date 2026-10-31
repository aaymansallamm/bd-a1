
1. **Observation:** ham is the majority class.
   **Evidence:** 4,516 ham and 653 spam messages.
   **Interpretation:** accuracy should be considered alongside spam precision and recall.

2. **Observation:** average cleaned message lengths differ between the groups.
   **Evidence:** spam = 112.44 characters; ham = 66.06.
   **Interpretation:** this is consistent with different communication styles, but length
   alone cannot reliably identify spam.

3. **Observation:** average cleaned word counts differ between the groups.
   **Evidence:** spam = 22.17 words; ham = 14.01.
   **Interpretation:** promotional and personal messages use different amounts of text.

4. **Observation:** spam has recurring high-frequency vocabulary after stop-word removal.
   **Evidence:** its top ten words are free, p, u, txt, ur, stop, mobile, text, claim, reply.
   **Interpretation:** these words suggest promotional or reply-request language;
   their presence alone is not proof that a message is spam.

5. **Observation:** ham has a different frequent-word profile.
   **Evidence:** its top ten words are u, im, just, ok, dont, got, know, like, good, come.
   **Interpretation:** the vocabulary is consistent with everyday conversation and can
   help explain why text features distinguish the two labels in this dataset.
