
### Iterative Planning[](https://microsoft.github.io/ai-agents-for-beginners/07-planning-design/#iterative-planning)

Some tasks require a back-and-forth or re-planning, where the outcome of one subtask influences the next. For example, if the agent discovers an unexpected data format while booking flights, it might need to adapt its strategy before moving on to hotel bookings.

Additionally, user feedback (e.g. a human deciding they prefer an earlier flight) can trigger a partial re-plan. This dynamic, iterative approach ensures that the final solution aligns with real-world constraints and evolving user preferences.

GitHub Copilot CLI supports the following types of custom instructions.

### [Repository-wide custom instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions#repository-wide-custom-instructions)

These apply to all requests made in the context of a repository.

These are specified in a  `copilot-instructions.md`  file in the  `.github`  directory at the root of the repository. See  [Creating repository-wide custom instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions#creating-repository-wide-custom-instructions).

### [Path-specific custom instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions#path-specific-custom-instructions)

These apply to requests made in the context of files that match a specified path.

These are specified in one or more  `NAME.instructions.md`  files within or below the  `.github/instructions`  directory at the root of the repository, or within or below a  `.github/instructions`  directory in the current working directory. See  [Creating path-specific custom instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions#creating-path-specific-custom-instructions).

If the path you specify in these instructions matches a file that Copilot is working on, and a repository-wide custom instructions file also exists, then the instructions from both files are used. You should avoid potential conflicts between instructions as Copilot's choice between conflicting instructions is non-deterministic.

### [Agent instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions#agent-instructions)

These are used by various AI agents.

You can create one or more  `AGENTS.md`  files. These can be located in the repository's root directory, in the current working directory, or in any of the directories specified by a comma-separated list of paths in the  `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`  environment variable.

Instructions in the  `AGENTS.md`  file in the root directory, if found, are treated as primary instructions. If an  `AGENTS.md`  file and a  `.github/copilot-instructions.md`  file are both found at the root of the repository, the instructions in both files are used.

Instructions found in other  `AGENTS.md`  files are treated as additional instructions. Any primary instructions that are found are likely to have more effect on Copilot's responses than additional instructions.

For more information, see the  [agentsmd/agents.md repository](https://github.com/agentsmd/agents.md).

Alternatively, you can use  `CLAUDE.md`  and  `GEMINI.md`  files. These must be located at the root of the repository.

### [Local instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions#local-instructions)

These apply within a specific local environment.

You can specify instructions within your own home directory, by creating a file at  `$HOME/.copilot/copilot-instructions.md`.

You can also set the  `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`  environment variable to a comma-separated list of directories. Copilot CLI will look for an  `AGENTS.md`  file, and any  `.github/instructions/**/*.instructions.md`  files, in each of these directories.

## [Creating repository-wide custom instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions#creating-repository-wide-custom-instructions)

1.  In the root of your repository, create a file named  `.github/copilot-instructions.md`.
    
    Create the  `.github`  directory if it does not already exist.
    
2.  Add natural language instructions to the file, in Markdown format.
    
    Whitespace between instructions is ignored, so the instructions can be written as a single paragraph, each on a new line, or separated by blank lines for legibility.
    
    For help on writing effective custom instructions, see  [About customizing GitHub Copilot responses](https://docs.github.com/en/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions).
    

## [Creating path-specific custom instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions#creating-path-specific-custom-instructions)

1.  Create the  `.github/instructions`  directory if it does not already exist.
    
2.  Optionally, create subdirectories of  `.github/instructions`  to organize your instruction files.
    
3.  Create one or more  `NAME.instructions.md`  files, where  `NAME`  indicates the purpose of the instructions. The file name must end with  `.instructions.md`.
    
4.  At the start of the file, create a frontmatter block containing the  `applyTo`  keyword. Use glob syntax to specify what files or directories the instructions apply to.
    
    For example:
    
    ```markdown
    ---
    applyTo: "app/models/**/*.rb"
    ---
    
    ```
    
    You can specify multiple patterns by separating them with commas. For example, to apply the instructions to all TypeScript files in the repository, you could use the following frontmatter block:
    
    ```markdown
    ---
    applyTo: "**/*.ts,**/*.tsx"
    ---
    
    ```
    
    Glob examples:
    
    -   `*`  - will all match all files in the current directory.
    -   `**`  or  `**/*`  - will all match all files in all directories.
    -   `*.py`  - will match all  `.py`  files in the current directory.
    -   `**/*.py`  - will recursively match all  `.py`  files in all directories.
    -   `src/*.py`  - will match all  `.py`  files in the  `src`  directory. For example,  `src/foo.py`  and  `src/bar.py`  but  _not_  `src/foo/bar.py`.
    -   `src/**/*.py`  - will recursively match all  `.py`  files in the  `src`  directory. For example,  `src/foo.py`,  `src/foo/bar.py`, and  `src/foo/bar/baz.py`.
    -   `**/subdir/**/*.py`  - will recursively match all  `.py`  files in any  `subdir`  directory at any depth. For example,  `subdir/foo.py`,  `subdir/nested/bar.py`,  `parent/subdir/baz.py`, and  `deep/parent/subdir/nested/qux.py`, but  _not_  `foo.py`  at a path that does not contain a  `subdir`  directory.
5.  Optionally, to prevent the file from being used by either Copilot cloud agent or Copilot code review, add the  `excludeAgent`  keyword to the frontmatter block. Use either  `"code-review"`  or  `"coding-agent"`.
    
    For example, the following file will only be read by Copilot cloud agent.
    
    ```markdown
    ---
    applyTo: "**"
    excludeAgent: "code-review"
    ---
    
    ```
    
    If the  `excludeAgent`  keyword is not included in the front matterblock, both Copilot code review and Copilot cloud agent will use your instructions.
    
6.  Add your custom instructions in natural language, using Markdown format. Whitespace between instructions is ignored, so the instructions can be written as a single paragraph, each on a new line, or separated by blank lines for legibility.
    

Did you successfully add a custom instructions file to your repository?

## [Custom instructions in use](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions#custom-instructions-in-use)

The instructions in the file(s) are available for use by Copilot as soon as you save the file(s). Instructions are automatically added to requests that you submit to Copilot.


[Zencoder](https://zencoder.ai/)  AI agents use detailed prompts, known as **Instructions**, to define their behavior, persona, and constraints. These agents are designed for specific software engineering tasks, such as coding, testing, or documentation, and can be customized within the  Zencoder platform.

![Zencoder Docs](data:image/png;base64,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)Zencoder Docs +1

Here are the key components and examples of Zencoder agent prompts:

1. Structure of a Zencoder Agent Prompt (Instructions)

When creating a custom agent, instructions should be specific and structured:

-   **Persona/Role:**  Define who the agent is (e.g., "You are a SecuritySentinel, a security auditing specialist...").
-   **Task Breakdown:**  A numbered list of steps or approaches the agent should follow.
-   **Context Utilization:**  Instructions to use Repo Grokking™ to understand file relationships and dependencies.
-   **Output Guidelines:**  Specific constraints on format, style, or required information.
    
    ![Zencoder Docs](data:image/png;base64,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)Zencoder Docs +3
    

2. Examples of Agent Prompt Instructions

-   **Repository Analyzer Agent:**  "Consider the currently open project and repository. Understand its structure, mutual relations between files, folders, dependencies, and more. Please provide me with an in-depth structured understanding of this repo".
-   **Spec-Driven Development Agent:**  "Always check for an existing specification in  `.zencoder/specs/`. If no spec exists, request one before proceeding. Validate implementation plans against specifications".
-   **Unit Testing Agent:**  "Generate unit tests for the [function name] function, ensuring 95% coverage. Include parameter descriptions with types, return value details, and exception information".
    
    ![Zencoder Docs](data:image/png;base64,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)Zencoder Docs +4
    

3. Core Agent Prompts

Zencoder has default agents with pre-defined prompts:

-   **Coding Agent:**  Focuses on creating and modifying multiple files, implementing features, and running validations.
-   **Ask Agent:**  A read-only agent used for "how-to" questions, debugging, and code explanation based on the current repo context.
-   **Repo Info Agent:**  Analyzes the entire codebase to build a context map (repo.md) for other agents to use.
    
    ![Zencoder Docs](data:image/png;base64,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)Zencoder Docs +4
    

4. Prompt Best Practices for Zencoder

-   **Use Action-Oriented Language:**  Use clear verbs like  _analyze, generate, refactor, or optimize_  to remove ambiguity.
-   **Provide Full Context:**  Include relevant code snippets and describe the expected behavior and constraints.
-   **Implement "Zen Rules":**  Create markdown files (e.g.,  `.zencoder/rules/sdd-standards.md`) that act as auto-applied instructions for consistent behavior across tasks.
-   **Few-Shot Prompting:**  Provide examples of input and output within the instructions for better consistency.
    
    ![Zencoder Docs](data:image/png;base64,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)Zencoder Docs +2
    

You can find pre-built agent prompts and community-contributed agents in the  **Zen Agents Marketplace**.

![Zencoder](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAIAAAACACAMAAAD04JH5AAAAKlBMVEVHcEzySgfySgfySgfySgfySgfySgfySgfySgfySgfySgfySgfySgfySgfOkmG/AAAADXRSTlMADtZFH7ww6IT0nl9yApNkbAAABSBJREFUeJztW9mCqyoQHEQWF/7/d2/UgIBAV5OQl3P7daIU3dW78/fXJbPcD9nk3Pf8RyL2yUWyqP2nKMTqnmLszzBsheNPmbafnG9r5x+2+AGEvXH+YQk5+Py5ff5L1rEAWgbwdhiqhIkG4Nw+7nyxIAAGmoGmwGAEKABnBwEQKAA3KCIIAyPQYxAoGMA0BgAQB7yMcUaNA1jEEARQJBqpAoYNxrAAjgRulCOUyqGKjImHDBUM8kRcBYP8gKGCQXUqroJBpcmMFQXuGxlJCC2ehiQK0y9pQGzrdF11mdbsTWg4LAOAqCmztLckjQ+aER4kFJsyy+tCFDu3UtpfI+SgEbK7ChvY04xRuqbhKLtAdUEWiLaYvKauhIaXqXAlgdBANd9bocLcLLqWkF8QX4y98InYFM+X1Esl/MuEg7qAt8SDautdQED+NqKALOrrma6B86PHqF/fFqho62EE6HxnAnvaznjnwmrYyAIVYNVTVgxxaI5K9r8k9VM8zUoEQVBUq5lJggHe9ET+XWbXIYEBrYgRt5CMWi+mby1sKui9kQ0YHUcKvDi0c5M3AEHsHgO4nDyF1BXOpwJmoBNcY5xi0jgu1uyYcD6ZtIIuWee7JU9lcwLhthAZWTxVGA1XEcBVapx/M1G1QY+UvDF551cSmdBSyiS+A54FaioTVTr/KYhnXb9k9NyngOU2UjaddOV0vIcYrOWCcsv5KiYF0ckDVDeKDguATS8WW08AvPPRuQM2UDsAMCkAGgB86/FTtBC5BB3CgsQ6fsqKAvAQGCPWGdI4iQgevYHpXfEAMDZCoAUsywSKMXIBXfu8EUjChTP5BFcrVxjAlgBkT58IeKl3TAPKMcWceoJmffsUyULFHvaAHPRabRps4d7+EKzID1ml4bRT31YcSwR0+5qPx74LIK7s5scTZrKyf9YLmSAtrLRV5uTCYiZlN/3ZoBmJrs+6QsynfGPGjcSBQYu9S4BIyFiuCr1Za3cWJUgSgKX9S+TqIyUnIlDpGN6mbGlaw/XWjoUTqE398E58EdUKBaj+S86EI6jTANSjKN8Bt8JWdgWUSXOtqMHDs7CF4Qn6eH24wlkHCqmiexhlYT9qTfd4W2kxy21/hZFdsmJsq6gEu+OPpB3Ixn8yRuSSQUvhSKiqvkgloV/G3u3xlaX+MKWSyfQZC/SuYtq+ior1g5qCbGtyG+RrRg9D2a6aDmjtk0q1EjIv6akrgXoq9gNNNTbs702BkjZKSfWFyC3Lyqq/gDdGJAD7YEZviTSr90YKn6+sqFNAw42gUsaMD/3oFmqtPbVZm5Zo+fwxAB+KuIN+hIwQAO8G+Pd9bwGiAgTAl5XcQTuCADLr2w+h7wgyIf0Rmhl5AKxtW/JoQ5AJl+nXAG0EhFi+LOsBQLYliGv5xRnbC1xt5xUJwkIPgLNyDkIBQEjgAXAXbhgA4FqeA9yF1yELCQCwQbBjBwuB3ph+a3Bm7srNQW0FbdlwC74NaAv8AfPruyZjOyLUWpIquN/CqwjgtopiQRROmSoAa0PKtPF3Yqzz4S6hze6kQ+c4AqNLaRohdWXcCJwuqVkWpN0pWhVAFeEtrdIse1N1oJUI+2P3ui8+EmqzP30/09EoV7vUwl0IJrK2jjSCki2rH+Met+/+d4+yFSrpTNY+bPvkE+8iE6tknm2OoX/v5aVQ9zanxULaVU1mmSa18nYcVcmJgO86viXpJ4O/P/+Q8J/Ov/jH2rIIeUych67a/pd/Vv4Dz0qEWhVkzvoAAAAASUVORK5CYII=)Zencoder

If you make changes to your custom instructions during a CLI session, your changes are available for use by Copilot the next time you submit a prompt in the current, or future, sessions.
