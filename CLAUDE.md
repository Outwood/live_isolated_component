# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

LiveIsolatedComponent is an Elixir library for testing Phoenix LiveView stateful and function components in isolation while maintaining interactivity. It wraps components in a mock LiveView, allowing tests to interact with them as if they were full LiveViews.

## Common Commands

```bash
# Run all library tests
mix test

# Run a single test file
mix test test/live_isolated_component/store_agent_test.exs

# Run tests with specific line
mix test test/live_isolated_component/store_agent_test.exs:10

# Run the test_app integration tests (from repo root)
cd test_app && mix deps.get && mix test

# Check formatting
mix format --check-formatted

# Run Credo linter
mix credo --strict

# Compile with warnings as errors (used in CI)
mix compile --error-on-warnings
```

## Architecture

### Core Flow

1. `live_isolated_component/2` macro (lib/live_isolated_component.ex) - Entry point that wraps a component in a mock LiveView using Phoenix's `live_isolated/3`
2. `StoreAgent` (lib/live_isolated_component/store_agent.ex) - Agent process that stores component state, assigns, slots, and callbacks. Used because functions aren't serializable across process boundaries
3. `View` module (lib/live_isolated_component/view.ex) - Base module for mock LiveViews with `use LiveIsolatedComponent.View` macro
4. `ViewUtils` (lib/live_isolated_component/view_utils.ex) - Shared utilities for mount, render, handle_info, and handle_event
5. `Hooks` (lib/live_isolated_component/hooks.ex) - LiveView lifecycle hooks that spy on handle_event/handle_info for test assertions

### Key Concepts

- **Slots**: Defined using the `slot/2` macro which wraps HEEx templates with special handling for `let` bindings
- **Assertions**: `assert_handle_event`, `assert_handle_info`, `refute_handle_event`, `refute_handle_info` macros use message passing to verify component behavior
- **live_assign/2,3**: Updates component assigns by sending messages to the mock LiveView process

### Test App

The `test_app/` directory contains a full Phoenix application used for integration testing. Tests in `test_app/test/` exercise the library against real LiveView components.

## Version Compatibility

- Elixir 1.17+, OTP 27+
- Phoenix 1.7.x or 1.8.x
- Phoenix LiveView 1.0.x or 1.1.x
