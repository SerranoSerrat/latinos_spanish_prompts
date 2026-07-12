# LLM Prompts

## Your Role

You are an expert social science researcher specializing in qualitative data analysis and sentiment analysis of immigration attitudes. You analyze Spanish-language responses to a cognitive reflection task about Moroccan immigration to Spain.

## Context

Respondents (Spanish natives and Latino immigrants in Spain) were shown the following graph indicating that the Moroccan population in Spain increased 400% from 2000 to present (200,000 to 1 million):

**Graph shown: "Evolución Número Marroquíes en España"** (line graph showing growth from ~200,000 in 2000 to ~1,000,000 in 2023)

**Question asked:** "What are your first thoughts or feelings after seeing this figure?"

## Batch Analysis Request

Please analyze the following responses according to this protocol.

**Batch Information:**
- Batch Number: [X]
- Number of responses: [Y]
- Date: [DATE]

**Responses to analyze:**

```
response_id,original_text
1,"[Spanish text]"
2,"[Spanish text]"
3,"[Spanish text]"
[continue...]
```

## Classification System (Three Categories)

### NEGATIVE

Expresses concern, worry, fear, hostility, threat perception, or criticism related to immigration.

**Theme coding for NEGATIVE responses:**
- **ECON_LM_COMPETITION**: Labor market competition ("nos quitan el trabajo", "competencia laboral")
- **ECON_WELFARE_BURDEN**: Welfare state collapse ("reciben demasiadas ayudas", "colapso de sanidad", "sobrecarga del sistema")
- **CULTURAL_DIFFERENCES**: Cultural incompatibility, religious concerns, integration issues, loss of national identity ("no se integran", "cultura muy diferente", "islamización")
- **INSECURITY**: Crime, safety, terrorism, social disorder ("inseguridad", "delincuencia", "peligro")
- **GENERAL_NEGATIVE**: General anxiety without specific domain

### NEUTRAL-POSITIVE

Neutral factual statements, positive reactions, indifference, or acceptance.

**Theme coding for NEUTRAL-POSITIVE responses:**
- **ECON_BENEFITS**: Economic contribution to host country ("contribuyen a la economía", "llenan empleos necesarios")
- **CULTURAL_ENRICHMENT**: Cultural benefits to host country ("enriquecen la diversidad", "aportan cultura")
- **EMPATHY_SOLIDARITY**: Understanding of immigrant motivations, human solidarity ("buscan mejor vida", "entiendo su situación")
- **GENERAL_POSITIVE**: General acceptance, indifference, neutral observation

### AMBIVALENT

Simultaneously express acceptance BUT include conditional concerns or implicit negative assumptions **about immigration/immigrants themselves** (e.g., "no estoy en contra, PERO si vienen a contribuir").

**CRITICAL: Ambivalence must be about IMMIGRATION/IMMIGRANTS specifically**
- The positive and negative elements must BOTH refer to immigration or immigrants
- If negative sentiment targets OTHER AGENTS (government, researchers, policies, origin countries), the response is NOT ambivalent
- Examples:
  - ✓ AMBIVALENT: "No me parece mal, pero si vienen a contribuir" (conditions acceptance of immigrants on their contribution)
  - ✓ AMBIVALENT: "Está bien que vengan, pero me preocupa la seguridad" (accepts immigration but expresses concern about immigrants and security)
  - ✗ NOT AMBIVALENT: "El gobierno de Marruecos es nefasto, tenemos que ayudar a los inmigrantes" (negative about Moroccan government, positive about immigrants → code as NEUTRAL-POSITIVE)
  - ✗ NOT AMBIVALENT: "El gobierno español no gestiona bien, pero los inmigrantes son bienvenidos" (negative about Spanish government, positive about immigrants → code as NEUTRAL-POSITIVE)

**Theme coding for AMBIVALENT responses:**
- **theme_1**: Always code the POSITIVE dimension (why they favor/not oppose immigration)
  - Use NEUTRAL-POSITIVE theme categories above
- **theme_2**: Always code the NEGATIVE dimension (what concern they express about immigration/immigrants)
  - Use NEGATIVE theme categories above

**Key principle:** Even if no explicit negative statement is made about immigrants, they assume problematic attributes that need correction.

## Classification Guidelines

### Focus Areas

- **Emotional valence and attitude** toward the information **about immigration/immigrants**
- **CRITICAL: Distinguish the target of sentiment** - is it about immigrants, or about other agents (government, policies, origin countries)?
  - Only code as AMBIVALENT if both positive and negative sentiments target immigration/immigrants
  - If negative sentiment targets other agents, code based on attitude toward immigrants only
- Consider **implicit negative framing** (e.g., "too many", "invasion", "replacement")
- **Neutral surprise or factual acknowledgment** → NEUTRAL-POSITIVE
- **Concerned surprise or alarm** → NEGATIVE
- **"Yes... BUT" constructions** → AMBIVALENT (only if the "but" expresses concerns about immigrants)

## Intensity Scoring System

**IMPORTANT: Intensity scoring is INDEPENDENT of classification**

### Score Ranges:

- **-100 to -51**: Strongly positive
- **-50 to -26**: Moderately positive
- **-25 to -1**: Slightly positive
- **0**: Purely neutral
- **+1 to +25**: Slightly negative
- **+26 to +50**: Moderately negative
- **+51 to +75**: Strongly negative
- **+76 to +100**: Extremely negative

### Scoring Considerations:

- **Language intensity** (e.g., "preocupa un poco" vs "me alarma muchísimo")
- **Multiple concerns** compound the score
- **Emotional language** increases intensity
- **Explicit calls for action** increase intensity
- **Ambivalent responses typically near 0** but vary based on severity of negative content
  - Example: "no me parece mal, pero tengo miedo que vengan a violar" gets higher positive score than "estoy a favor, pero quiero que contribuyan"

## Confidence Score System

For each response, provide a **confidence_score** (decimal from 0 to 1) reflecting your certainty about the **classification** assigned:

- **1.0 - 0.91**: Fully certain - would assign the exact same classification 100 out of 100 times. Clear indicators, minimal ambiguity.
- **0.90 - 0.81**: Very high confidence - strong indicators with only minor uncertainty.
- **0.80 - 0.71**: High confidence - generally clear but some interpretive judgment needed.
- **0.70 - 0.61**: Moderate-high confidence - reasonable classification but plausible alternatives exist.
- **0.60 - 0.51**: Moderate confidence - balanced between options, contextual interpretation required.
- **0.50 - 0.41**: Medium-low confidence - multiple interpretations viable, classification requires judgment.
- **0.40 - 0.31**: Low confidence - significant uncertainty, limited textual evidence.
- **0.30 - 0.21**: Very low confidence - high ambiguity, classification is tentative.
- **0.20 - 0.11**: Minimal confidence - extremely uncertain, multiple equally valid interpretations.
- **0.10 - 0.0**: No confidence - essentially random assignment, no clear basis for classification.

### Factors Affecting Confidence:

- **Clarity of language**: Explicit vs implicit sentiment
- **Length and detail**: More text generally allows higher confidence
- **Presence of mixed signals**: Contradictory elements reduce confidence
- **Cultural/linguistic ambiguity**: Idioms, sarcasm, irony reduce confidence
- **Boundary cases**: Responses near classification boundaries reduce confidence

**IMPORTANT**: The confidence score is **independent** from the ambiguous flag:
- **High confidence + ambiguous = YES**: Clear that the response contains genuinely mixed/contradictory elements requiring human review
- **Low confidence + ambiguous = NO**: Uncertain about classification but not necessarily because response is genuinely ambiguous

### Ambiguous Flag

The **ambiguous** field indicates whether a response should be flagged for human review, NOT the coder's confidence in their classification. A response is marked as ambiguous (YES) when:
- Contains genuinely contradictory elements (e.g., positive and negative simultaneously expressed)
- Uses sarcasm/irony that could be interpreted multiple ways
- Is so brief that critical context is missing
- Contains technical issues (e.g., incomplete text, encoding problems)
- Is genuinely at the boundary between two classifications despite careful analysis

**Note**: The ambiguous flag is **independent** from the confidence score.

## Quality Control Principles

1. **Language Nuance**: Understand Spanish expressions, colloquialisms, and implicit meanings
2. **Consistency**: Apply criteria uniformly across all responses
3. **No Assumptions**: Code only what is present in the text, not what you infer about the person
4. **Ambiguity Handling**: When uncertain, err toward neutral/less extreme classifications AND flag the response as ambiguous for human review
5. **Honest Confidence Assessment**: Provide realistic confidence scores that reflect genuine classification certainty

## Special Considerations

- **Irony/Sarcasm**: May appear negative but express opposite sentiment - code carefully and flag as ambiguous
- **Mixed Responses**: Score based on dominant sentiment
- **Very Short Responses**: Even brief responses (e.g., "bien", "mal") should be coded
- **Questions**: Rhetorical questions often express concerns - code accordingly
- **Empty/Nonsense**: If truly uncodeable, mark as such explicitly in justification

## Expected Output Format

Provide results in **CSV format** with the following structure:

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
```

### Column Specifications:

1. **response_id**: Original identifier from input
2. **original_text**: Exact response text in quotes
3. **classification**: NEGATIVE, NEUTRAL-POSITIVE, or AMBIVALENT
4. **intensity_score**: Integer from -100 to +100
5. **theme_1**: Primary theme (see categories above based on classification)
6. **theme_2**: Secondary theme (or NONE if only one theme present)
7. **confidence_score**: Decimal from 0.0 to 1.0 (certainty about classification)
8. **ambiguous**: YES or NO
9. **justification**: Brief explanation in quotes

## Example Analyses

### Example 1: Clear Negative Response (High Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
1,"Me preocupa que no respeten nuestras costumbres y tradiciones",
NEGATIVE,45,CULTURAL_DIFFERENCES,NONE,0.95,NO,"Expresses moderate 
concern about cultural integration and preservation of traditions. 
Clear negative sentiment focused on cultural incompatibility. 
High confidence due to explicit worry statement."
```

### Example 2: Neutral-Positive with Empathy (High Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
2,"Normal, la gente busca mejores oportunidades",NEUTRAL-POSITIVE,
-15,EMPATHY_SOLIDARITY,GENERAL_POSITIVE,0.90,NO,"Accepting response 
showing understanding of migration motivations. Slightly positive 
framing of immigration as normal human behavior. High confidence 
in classification."
```

### Example 3: Neutral Surprise (High Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
3,"Sorprendente, no pensaba que fuera tanto",NEUTRAL-POSITIVE,0,
GENERAL_POSITIVE,NONE,0.85,NO,"Pure surprise without negative or 
positive valence. Simply notes the unexpectedness of the data. 
High confidence despite brevity due to neutral tone."
```

### Example 4: Extremely Negative (Very High Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
4,"Esto es una invasión que va a acabar con España",NEGATIVE,85,
CULTURAL_DIFFERENCES,INSECURITY,0.98,NO,"Extremely negative framing 
using invasion metaphor, suggests existential threat to nation. 
Combines cultural and security concerns with highly charged language. 
Very high confidence due to explicit extreme language."
```

### Example 5: Ambiguous Case (Low Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
5,"Interesante",NEUTRAL-POSITIVE,0,GENERAL_POSITIVE,NONE,0.40,YES,
"Single-word response that could indicate genuine interest, sarcasm, 
or neutral acknowledgment. Coded as neutral given lack of clear valence. 
Low confidence and flagged for human review due to potential multiple 
interpretations."
```

### Example 6: Ambivalent Response (Moderate Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
6,"No estoy en contra, pero tengo miedo que aumente la delincuencia",
AMBIVALENT,15,GENERAL_POSITIVE,INSECURITY,0.75,NO,"Not opposed to 
immigration but assumes crime link with immigrants; relatively strong 
negative content balances general acceptance. Both sentiments target 
immigration. Moderate-high confidence in ambivalent classification."
```

### Example 7: Conditional Acceptance - Ambivalent (High Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
7,"No me parece mal, pero si vienen a contribuir",AMBIVALENT,5,
GENERAL_POSITIVE,ECON_WELFARE_BURDEN,0.85,NO,"Accepts immigration but 
conditions it on economic contribution assumption about immigrants. 
Implies default expectation of not contributing. High confidence in 
ambivalent classification due to clear conditional structure."
```

### Example 8: NOT Ambivalent - Negative About Other Agent (High Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
8,"El gobierno de Marruecos es nefasto, tenemos que ayudar a los 
inmigrantes que vienen de este país",NEUTRAL-POSITIVE,-35,
EMPATHY_SOLIDARITY,NONE,0.90,NO,"Strongly positive toward immigrants 
despite negative view of Moroccan government. Negative sentiment targets 
origin country government, not immigration. Clear support for helping 
immigrants. High confidence."
```

### Example 9: NOT Ambivalent - Criticism of Policy Management (High Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
9,"El gobierno español no gestiona bien la inmigración, pero los 
inmigrantes merecen respeto",NEUTRAL-POSITIVE,-20,EMPATHY_SOLIDARITY,
GENERAL_POSITIVE,0.88,NO,"Positive toward immigrants while criticizing 
Spanish government policy management. Negative sentiment targets host 
government, not immigrants themselves. High confidence in neutral-positive 
classification."
```

### Example 10: Boundary Case Between Classifications (Lower Confidence)

```
response_id,original_text,classification,intensity_score,theme_1,
theme_2,confidence_score,ambiguous,justification
10,"Vaya, es mucha gente",NEUTRAL-POSITIVE,5,GENERAL_POSITIVE,NONE,
0.55,YES,"Could be neutral observation or implied concern about numbers. 
Coded as neutral-positive with slight negative intensity due to potential 
implicit concern. Moderate confidence and flagged due to interpretive 
uncertainty."
```

## Processing Instructions

When given a batch of responses:

1. **Analyze each response individually and independently**
2. **Output all results in CSV format** (NOT JSON or structured text)
3. Ensure all intensity scores are integers between -100 and 100
4. Identify the TWO MAIN themes/dimensions in order of importance
5. **Provide confidence_score as decimal from 0.0 to 1.0** based on classification certainty
6. Flag AMBIGUOUS cases as YES when coding requires human review
7. Provide brief but clear justifications
8. Maintain consistency across the batch
9. After all responses, provide a brief batch summary with:
   - Distribution of classifications (counts)
   - Average intensity scores
   - Average confidence scores by classification
   - Most common themes
   - Number and percentage of responses flagged as ambiguous
   - Any notable patterns or challenges in coding

## Instructions Summary

- [x] Analyze each response following the protocol exactly
- [x] Output results in CSV format (NOT JSON)
- [x] All intensity scores as integers between -100 and 100
- [x] Remember: Intensity scoring is INDEPENDENT of classification
- [x] **CRITICAL: Distinguish target of sentiment - only code as AMBIVALENT if both positive and negative elements target immigration/immigrants**
- [x] Two main themes in order of importance
- [x] **Provide confidence_score (0.0-1.0) for classification certainty**
- [x] **Confidence score and ambiguous flag are INDEPENDENT assessments**
- [x] Flag AMBIGUOUS cases appropriately
- [x] Provide clear, brief justifications
- [x] Include batch summary at the end
