 ## Dataset
    Use `dts_106_sales_leads`. Calendly facts live on `fct_calendly_bookings` (grain: event x attendee x rep).

    ## Step-by-step
    1. Pull metric `count_calls` — defined as `count_distinct(fct_calendly_bookings.event_id)`, so a single event with multiple attendees still counts as 1.
    2. Break down by `fct_calendly_bookings.rep_name` (rows) and `fct_calendly_bookings.start_date | datetrunc('week')` or `month` (columns) — pick the granularity the user asked for; default to month.
    3. To split demo vs onboarding, dimensionalize or filter on `fct_calendly_bookings.primary_call_category`:
       - `'Sales'` → demo calls
       - `'Retention'` → onboarding calls
       - `'Solo'`, `'Internal'`, `'Marketing Assets'`, `'Uncategorized'` → exclude unless the user asks for them.
    4. Default time range: last 4 quarters unless the user specifies otherwise.
    5. Default rep filter: include every `rep_name`; exclude only when the user asks (e.g., "exclude Janice" → filter out `'Holistics Software'`, which is Janice's Calendly account).

    ## Output format
    Default to a `PivotTable`:
    - rows: `fct_calendly_bookings.rep_name`
    - columns: `fct_calendly_bookings.start_date` truncated to the requested period
    - values: `count_calls`
    - breakdown (optional, only when user asks for the demo/onboarding split): add `fct_calendly_bookings.primary_call_category` as a second row dimension
    - settings: `show_row_total: true`, `show_column_total: true`

    For a single-period question (e.g., "this quarter"), output a `DataTable` with `rep_name` and `count_calls` sorted desc.

    ## Caveats
    - `count_calls` deduplicates by event. For "unique invitees" use `count(fct_calendly_bookings.attendee_id)` ad-hoc; for "unique attendee emails" use `count_distinct(fct_calendly_bookings.attendee_email)`.
    - Calendly does NOT auto-mark no-shows; `fct_calendly_bookings.is_no_show` is reliable only when explicitly populated by the rep.
    - Rep `'Holistics Software'` is Janice's Calendly account.
    - Cancelled events: by default include them (they're real bookings). Exclude only when asked — filter `fct_calendly_bookings.is_cancelled == false`.

    ## Worked examples
    **User**: "How many demo calls did each rep take in the last 3 months?"
    → PivotTable on `dts_106_sales_leads`
       - filter: `start_date` matches "last 3 months" AND `primary_call_category == 'Sales'`
       - rows: `rep_name`
       - values: `count_calls`
       - sort: `count_calls` desc

    **User**: "Show demo vs onboarding split per rep, monthly, for last year"
    → PivotTable on `dts_106_sales_leads`
       - filter: `start_date` matches "last 12 months" AND `primary_call_category` in `('Sales', 'Retention')`
       - rows: `rep_name`, `primary_call_category`
       - columns: `start_date | datetrunc('month')`
       - values: `count_calls`
       - settings: row + column totals on
  ;;
