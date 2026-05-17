# Workflow Prompts

Multi-step workflow patterns.

## Contract Lifecycle

1. Generate: Claude writes the contract
2. Review: Manually check for issues
3. Deploy: Hermes or Remix handles deployment
4. Verify: Confirm on Etherscan
5. Record: Log everything

## Daily Session

1. Open template
2. Review yesterday's TODO
3. Set today's focus
4. Record as you go
5. Plan tomorrow

## Security Check

Before deploying:
- Access control on critical functions
- No hardcoded secrets
- Events on state changes
- Constructor correctness
