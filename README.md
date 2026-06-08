# GenAI assignment solution

Flow :

```mermaid
flowchart TD
    A([User question]) --> B[Gradio UI\nhandle_question]
    B --> C{ask_cpih}
    C --> D[Groq API\nLlama 3.3 70B]
    D --> E{Parse response}
    E -->|OUT_OF_SCOPE| F[Refusal card]
    E -->|Valid JSON| G[Extract answer\n+ numbers + code]
    G --> H{chart_type}
    H -->|trend_monthly| I[chart_trend_monthly\nmatplotlib]
    H -->|annual_bar| J[chart_annual_bar\nmatplotlib]
    H -->|comparison_bar| K[chart_comparison_bar\nmatplotlib]
    H -->|none| L[No chart]
    I & J & K & L --> M[Return to UI]
    M --> N[Answer card\n+ chart + code]

    style A fill:#7c6aff,color:#fff
    style F fill:#9b2335,color:#fff
    style N fill:#1a1d27,color:#e8e8f0
```
-----------------------------------------------------------------------------------------------------------------------------------------
Brief explanation of each stage:

User question → Gradio UI — the question is typed or clicked from examples and passed to handle_question().

ask_cpih() → Groq API — the full ONS dataset is embedded in the system prompt alongside strict JSON output instructions, then sent to Llama 3.3 70B.

Parse JSON response — the response is cleaned of markdown fences, the outermost {} block is extracted, and trailing commas are fixed. If the model prefixed OUT_OF_SCOPE:, the refusal path short-circuits here and shows a red card.

Extract answer + numbers + provenance code — the three fields from the JSON are pulled out. The code field is the auditable pandas snippet the model generated.

Select chart type — the model also returns a chart_type enum (trend_monthly, annual_bar, comparison_bar, or none) plus parameters. The correct matplotlib helper is called with those params against the real pandas DataFrames — not the model's stated numbers.

Answer + chart + provenance — all three are returned to the Gradio UI and rendered together.

-----------------------------------------------------------------------------------------------------------------------------------------

Solution reply Status - UNK
