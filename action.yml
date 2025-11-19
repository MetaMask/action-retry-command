name: Retry command
description: Retry a command up to a specified number of times.

inputs:
  command:
    description: The command to run.
    required: true
  shell:
    description: The shell to run the command in.
    required: false
    default: bash
  max-retries:
    description: The maximum number of times to retry the command.
    required: false
    default: 3

outputs:
  success:
    description: Whether the command succeeded.
    value: ${{ steps.success.outputs.success }}

runs:
  using: composite
  steps:
    - name: Retry command
      run: |
        command() {
          "$SHELL" -c "$COMMAND"
        }

        for i in $(seq 1 "$MAX_RETRIES"); do
          if command; then
            exit 0
          fi

          echo "Attempt $i failed, retrying in $((2 ** (i - 1))) seconds..."
          sleep $((2 ** (i - 1)))
        done
        echo "::error::Command failed after $MAX_RETRIES attempts."
        exit 1
      shell: ${{ inputs.shell }}
      env:
        # The command is provided as environment variables to avoid issues with
        # multi-line commands.
        COMMAND: ${{ inputs.command }}
        MAX_RETRIES: ${{ inputs.max-retries }}
    - name: Set success output
      id: success
      run: echo "success=true" >> "$GITHUB_OUTPUT"
      shell: bash
