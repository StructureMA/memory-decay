# Memory Decay: Teaching AI to Forget Like Humans Do

**An experiment in applying human memory patterns to AI**

Working with AI memory, we realized something: perfect memory isn't actually that useful. Humans forget things naturally - recent events stay vivid while old details fade unless they're reinforced. This isn't a bug, it's a feature. It helps us prioritize what matters.

So we asked: what if AI memory worked the same way?

## The Observation

We noticed something interesting: AI memory typically treats everything equally. A fact from yesterday has the same weight as something from last month. But human memory doesn't work that way at all.

Humans naturally forget. Recent things are vivid. Old details fade unless we revisit them. And we signal our uncertainty - "I think you said..." vs "You definitely told me..."

What would happen if we gave AI the same kind of memory?

## Why Would You Want AI to Forget?

Fair question. Here's what decay gives you:

**Honest uncertainty** - Instead of the AI confidently stating something it barely remembers, it can say "I think you mentioned..." when confidence is low. Less creepy, more honest.

**Recency matters** - What you talked about yesterday is more relevant than a casual mention from three months ago. Decay naturally prioritizes recent, reinforced information.

**Natural long-term conversations** - Over weeks or months, the AI won't have perfect recall of every detail. Just like a human friend, it might need a quick reminder. That's actually more comfortable.

**User control** - You decide what matters. Keep bringing something up and it stays strong. Let it fade and the AI stops treating it as gospel.

**Less weird** - Perfect recall forever is uncanny valley territory. Forgetting is human. It makes the interaction feel more natural.

## Our Solution: The Ebbinghaus Curve for AI

We implemented Hermann Ebbinghaus's forgetting curve - a mathematical model of how human memory decays over time. Combined with reinforcement learning principles, we created a system where:

1. **Memories decay naturally** - Each memory type has its own decay rate:
   - Facts (identity info): 0.01 (very slow decay)
   - Preferences: 0.05
   - Goals: 0.15
   - Events: 0.25
   - Context: 0.60 (fades quickly)

2. **Reinforcement strengthens memories** - Every time a memory is accessed or reinforced, the decay slows:
   ```
   adjusted_decay_rate = base_rate / (1 + 0.3 × reinforcement_count)
   ```

3. **Confidence reflects time** - Using the Ebbinghaus curve:
   ```
   confidence = initial_confidence × e^(-decay_rate × hours_elapsed)
   ```

4. **The AI knows when to verify** - Different confidence thresholds trigger different behaviors:
   - ≥0.7: High confidence, use directly
   - 0.5-0.7: Medium, present but note uncertainty
   - 0.3-0.5: Low, verify before using
   - <0.3: Archive or delete

## The Demo

We built an interactive visualization to show the system in action:

👉 **[Try the live demo →](https://structurema.github.io/memory-decay)**

Or download and open `index.html` locally - no dependencies needed.

Watch five different memory types decay over time. See how reinforcement slows the decay. Experience what it feels like when the AI says "I think you mentioned..." instead of claiming perfect recall.

## What We're Proud Of

This isn't just a technical implementation - it's a design philosophy. We believe AI should:
- Signal uncertainty honestly
- Prioritize recent, reinforced information
- Feel more natural in long-term conversations
- Give users agency to reinforce what matters

The system handles:
- **Hybrid migration** - existing memories get reasonable defaults
- **Runtime configuration** - change decay behavior without restarting
- **User control** - decide whether to archive, delete, or keep low-confidence memories
- **Verification hints** - the AI context includes confidence levels, so it can say "I think you mentioned Seattle?" instead of stating it as fact

## The Implementation

The core is surprisingly elegant. Here's the decay calculation:

```typescript
private calculateCurrentConfidence(memory: Memory): number {
  const hoursSinceReinforced =
    (Date.now() - new Date(memory.last_reinforced).getTime()) / (1000 * 60 * 60);

  const adjustedDecayRate =
    memory.decay_rate / (1 + 0.3 * memory.reinforcement_count);

  const confidence =
    memory.initial_confidence * Math.exp(-adjustedDecayRate * hoursSinceReinforced);

  return Math.max(0, Math.min(1, confidence));
}
```

Each memory tracks:
- `initial_confidence` - how sure we were when we learned it
- `decay_rate` - how fast it naturally fades
- `reinforcement_count` - how many times it's been reinforced
- `last_reinforced` - when we last accessed/reinforced it

When recalling memories, we calculate current confidence on-demand. The AI sees memories with confidence levels and can decide how to present them.

## Why This Matters

We think this makes AI conversations more human. Instead of:
> "You live in Seattle." (stated with unearned confidence)

You get:
> "I think you mentioned living in Seattle? Want to confirm before I assume?"

It's honest. It's humble. It respects that memory fades and that's okay.

## Try It Yourself

The demo is standalone HTML - no build step, no dependencies. Just open it and play:

```bash
open index.html
```

We hope you find this as interesting as we do! 

---

*Built by JL and CC.*
