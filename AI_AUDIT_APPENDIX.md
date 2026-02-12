# AI Audit Appendix (Assignment 04)

## Tool(s) Used
- ChatGPT, GitHub Copilot.

## Task(s) Where AI Was Used
- Initial part of the README document comprehension.
- Regression, slope, intercept explanation.
- Code generation.
- Some parts of the results interpretation. 

## Prompt(s)
- ChatGPT: I have a API key. Now, how do I link that code to my data?
- ChatGPT: Explain in short paragraphs what a regression, a slope, and an intercept are in the context of data analysis & statistics.
- GitHub Copilot: Load pre-built annual REIT data (REIT_sample_annual_*.csv). Then follow the steps:
    1. Read the CSV file.
    2. If 'year' is missing but 'date' exists, extract year from date.
    3. Rename ret12 to ret (annual return = 12-month cumulative return).
    4. Merge year-end interest rates using _merge_annual_interest_rates.
    5. Drop rows with missing ret, div12m_me, or prime_rate.
- GitHub Copilot: Drop rows with missing values in ret, div12m_me, prime_rate (after that prompt that, GitHub Copilot automatically implemented the remaining TODOs on its own).
- GitHub Copilot: Fill the coefficient comparison of all three regressions (so I can later interpret the results).

## Output Summary
- ChatGPT: The script was already designed to “link itself” to the data folder automatically. I just needed to paste the API key correctly.
- ChatGPT: regression models relationships between variables; slope is the change in the outcome for a one-unit change in the predictor; intercept is the predicted outcome when the predictor equals zero.
- GitHub Copilot: loaded the new data, created a spreadsheet with it, and generated the required regressions and scatterplots. Additionally, as asked in the README document, it made all file paths to be relative.
- GitHub Copilot: generated the entire code for all of the above requirements:
    df = df.dropna(subset=["ret", "div12m_me", "prime_rate"])

    return df


def estimate_regression(df: pd.DataFrame, x_var: str):
    """
    Estimate OLS regression: ret ~ x_var

    Parameters
    ----------
    df : pd.DataFrame
        REIT data with 'ret' and x_var columns.
    x_var : str
        Name of the X variable (e.g., 'div12m_me', 'prime_rate', 'ffo_at_reit').

    Returns
    -------
    statsmodels.regression.linear_model.RegressionResultsWrapper
        Fitted regression model.
    """
    model = ols(f"ret ~ {x_var}", data=df).fit()
    return model


def save_regression_summary(model, output_path: Path) -> None:
    """
    output_path.parent.mkdir(parents=True, exist_ok=True)
    with open(output_path, "w") as f:
        f.write(str(model.summary()))


def plot_scatter_with_regression(
    df: pd.DataFrame, model, x_var: str, title: str, xlabel: str, output_path: Path
) -> None:
    """
    """
    output_path.parent.mkdir(parents=True, exist_ok=True)
    fig, ax = plt.subplots(figsize=(10, 6))

    plot_df = df[["ret", x_var]].dropna()
    x = plot_df[x_var]
    y = plot_df["ret"]

    ax.scatter(x, y, alpha=0.35, label="Observations")

    x_min, x_max = x.quantile(0.02), x.quantile(0.98)
    x_grid = np.linspace(x_min, x_max, 200)
    intercept = model.params.get("Intercept", 0.0)
    slope = model.params.get(x_var, 0.0)
    y_hat = intercept + slope * x_grid
    ax.plot(x_grid, y_hat, color="crimson", linewidth=2.0, label="Fitted line")

    y_min, y_max = y.quantile(0.02), y.quantile(0.98)
    ax.set_xlim(x_min, x_max)
    ax.set_ylim(y_min, y_max)

    r2 = model.rsquared
    ax.set_title(f"{title} (R² = {r2:.3f})")
    ax.set_xlabel(xlabel)
    ax.set_ylabel("Annual Return")
    ax.legend()

    plt.savefig(output_path, dpi=300, bbox_inches="tight")
    plt.close(fig)


def print_key_results(model, x_var: str) -> None:
    """
    Print key regression results to the console.
    """
    print("\n" + "=" * 60)
    print(f"ret (annual) ~ {x_var.upper()}")
    print("=" * 60)
    intercept = model.params.get("Intercept", float("nan"))
    slope = model.params.get(x_var, float("nan"))
    se_intercept = model.bse.get("Intercept", float("nan"))
    se_slope = model.bse.get(x_var, float("nan"))
    t_intercept = model.tvalues.get("Intercept", float("nan"))
    t_slope = model.tvalues.get(x_var, float("nan"))
    p_intercept = model.pvalues.get("Intercept", float("nan"))
    p_slope = model.pvalues.get(x_var, float("nan"))

    print(f"Intercept (beta0): {intercept:.6f} | SE: {se_intercept:.6f} | t: {t_intercept:.3f} | p: {p_intercept:.4f}")
    print(f"Slope (beta1):     {slope:.6f} | SE: {se_slope:.6f} | t: {t_slope:.3f} | p: {p_slope:.4f}")
    print(f"R^2: {model.rsquared:.3f} | Adj R^2: {model.rsquared_adj:.3f} | N: {int(model.nobs)}")

    direction = "positive" if slope >= 0 else "negative"
    significance = "significant" if p_slope < 0.05 else "not significant"
    print(f"Slope is {direction} and {significance} at 5% level.")
    print("=" * 60 + "\n")


def _find_reit_data(data_dir: Path) -> Path:
    """Find REIT_sample_annual_*.csv in data folder (uses most recently modified)."""
    candidates = list(data_dir.glob("REIT_sample_annual_*.csv"))
    if not candidates:
        raise FileNotFoundError(
            f"No REIT_sample_annual_*.csv found in {data_dir}\n"
            "Use the pre-built annual dataset: REIT_sample_annual_2004_2024.csv"
        )
    return max(candidates, key=lambda p: p.stat().st_mtime)


def main() -> None:
    """Main execution function."""
    data_dir = DATA_DIR
    data_path = _find_reit_data(data_dir)
    results_dir = RESULTS_DIR

    print("Loading REIT annual data (REIT_sample_annual_*.csv)...")
    df = load_reit_annual_data(data_path)
    print(f"Loaded {len(df)} firm-year observations.")
    print(f"Years: {df['year'].min():.0f}–{df['year'].max():.0f}")

    for x_var, title, xlabel in REGRESSION_SPECS:
        print(f"\nEstimating regression: ret (annual) ~ {x_var}")
        model = estimate_regression(df, x_var)
        print_key_results(model, x_var)

        summary_path = results_dir / f"regression_{x_var}.txt"
        plot_path = results_dir / f"scatter_{x_var}.png"
        save_regression_summary(model, summary_path)
        print(f"Saved: {summary_path}")
        plot_scatter_with_regression(df, model, x_var, title, xlabel, plot_path)
        print(f"Saved: {plot_path}")
- GitHub Copilot: it dropped the rows with missing values.
- GitHub Copilot: it filled in the blanks in the coefficient comparison of all three regressions in the report so I could later interpret the results.

## Verification & Modifications (Disclose • Verify • Critique)
- **Verify:** How you checked the output (e.g., ran the script, spot-checked regression table)
    - I ran the fetch_interest_rates.py & the assignment04_regression.py scripts; I compared the scatterplots and the regression tables generated to make sure the values were correct; I committed & synched all changes to verify every scatterplot, regression table, and fragment of code was placed in its proper folder correctly formatted.  
- **Critique:** Everything was correct.
- **Modify:** From the AI-generated outputs, I did not change anything. I read them, tried to understand them, applied them, and used them as the foundaitons for the final report and result interpretation. 