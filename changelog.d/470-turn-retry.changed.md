- **Behavior change for recipes setting `agent.retry`:** non-empty retry
  settings now govern framework-wide turn retries as well as other provider
  calls (#470). Turns retain Membrane's default overloaded schedule unless
  overridden; omitted or empty retry settings leave the turn policy unchanged.
