# Stripe Radar: ML System Design Case Study

## What is Stripe Radar?

Stripe Radar is a **real-time fraud prevention system** that protects online payments by analyzing transactions in milliseconds to determine if they're legitimate or fraudulent. It's used by millions of businesses worldwide to prevent credit card fraud, stolen card usage, and other payment scams.

**Key Stats:**
- Analyzes **1,000+ characteristics** per transaction
- Makes decisions in **< 100 milliseconds**
- Processes **billions of transactions**
- **0.1% false positive rate** (blocks only 1 in 1000 legitimate transactions)
- Fraud rate: ~1 in 1,000 payments (making it a rare event detection problem)

## The Core Problem

### Why Fraud Detection is Hard

1. **Speed Requirement**: Must decide in < 100ms (faster than a blink of an eye)
2. **Scale**: Billions of transactions globally
3. **Accuracy**: Can't block legitimate customers (hurts business revenue)
4. **Rarity**: Fraud is rare (1/1000), making it a class imbalance problem
5. **Cost**: Must be cheap per transaction to be viable
6. **Evolving Threats**: Fraudsters constantly adapt their methods

### The Business Impact
- **False Positives**: Blocking legitimate customers = lost revenue
- **False Negatives**: Letting fraud through = chargebacks and losses
- **User Experience**: Customers expect seamless, fast checkout
- **Compliance**: Must meet financial regulations and standards

## System Architecture Evolution (7-Year Journey)

### Phase 1: Simple Models (Early Years)
**What they used**: Logistic regression and basic ML models
**Why**: Start simple, prove the concept works
**Limitations**: Not sophisticated enough for complex fraud patterns

### Phase 2: Wide & Deep Model (Middle Years)
**Architecture**: Combined two approaches
- **Wide Component (XGBoost)**: Memorization - remembers specific patterns
- **Deep Component (DNN)**: Generalization - learns abstract patterns

**How it worked**:
```
Transaction Features → [XGBoost + DNN] → Fraud Score
```

**Benefits**: Best of both worlds - memorization + generalization
**Problems**:
- XGBoost couldn't scale to billions of transactions
- Slow to retrain (days/weeks)
- Hard to experiment with new techniques
- Limited parallelization

### Phase 3: DNN-Only Architecture (Current - 2022)
**Big Change**: Removed XGBoost, went pure Deep Neural Network
**Challenge**: XGBoost removal would cause 1.5% performance drop
**Solution**: Multi-branch DNN inspired by ResNeXt

**New Architecture**:
```
Input Features → Multiple Branches → Summation → Fraud Score
```

**Why this works**:
- Each branch is like a small specialized network
- Branches can learn different patterns
- Summation combines all insights
- Much faster training and experimentation

## Technical Deep Dive

### How the Multi-Branch DNN Works

**The Problem**: How do you replace XGBoost's memorization power in a neural network?

**The Solution**: Create multiple specialized branches that each learn different patterns:

```
Transaction Features
    ↓
┌─────────┬─────────┬─────────┬─────────┐
│ Branch 1│ Branch 2│ Branch 3│ Branch N│
│(Patterns│(Velocity│(Location│(Custom │
│ & Rules)│ Patterns)│ Patterns)│ Patterns)│
└─────────┴─────────┴─────────┴─────────┘
    ↓
    Summation → Fraud Score (0-100)
```

**Why This Works**:
- **Branch 1**: Learns specific fraud patterns (like XGBoost memorization)
- **Branch 2**: Learns velocity patterns (unusual spending speed)
- **Branch 3**: Learns geographic patterns (suspicious locations)
- **Branch N**: Learns custom patterns for specific fraud types
- **Summation**: Combines all insights into final score

### What Features Does Radar Analyze?

**1,000+ characteristics per transaction**, including:

**Identity Verification**:
- Does cardholder name match email?
- Is this a new email address?
- Has this email been used for fraud before?

**Device & Location**:
- Is this a new device?
- Is the location suspicious?
- How many cards have been used from this IP?
- Is the transaction coming from a VPN/proxy?

**Behavioral Patterns**:
- How fast is this person spending? (velocity)
- Is this unusual spending for this card?
- What time of day is this transaction?
- Is this a repeat customer?

**Network Effects**:
- Have we seen this card number before?
- Is this merchant frequently targeted?
- Are there similar transactions happening now?

### Training Data Strategy

**The Big Insight**: More data often beats better algorithms

**Experiments**:
- **10x more data**: Significant performance improvements
- **100x more data**: Currently testing (promising results)
- **Key**: DNN-only architecture made this possible (faster training)

**Why This Matters**:
- More fraud examples = better pattern recognition
- More legitimate examples = fewer false positives
- Global network effects = unique competitive advantage

## Key Lessons Learned (7 Years of Building)

### Lesson 1: Don't Get Comfortable with Your ML Architecture

**The Journey**:
```
Year 1-2: Simple Models (Logistic Regression)
    ↓
Year 3-5: Wide & Deep (XGBoost + DNN)
    ↓
Year 6-7: DNN-Only (Multi-branch)
```

**Key Insight**: Each architectural change brought **leap-size improvements** in performance

**The XGBoost Problem**:
- XGBoost was great at memorizing specific patterns
- But it couldn't scale to billions of transactions
- Training took days/weeks
- Hard to experiment with new techniques
- Not parallelizable at scale

**The Solution**: Multi-branch DNN that could memorize AND generalize
- **Result**: Faster training, better scalability, more experimentation

### Lesson 2: Scale Training Data Strategically

**The Experiment**: What if we just use more data instead of better algorithms?

**Results**:
- **10x more data**: Significant performance improvements
- **100x more data**: Currently testing (very promising)
- **Key**: DNN-only architecture made this possible (much faster training)

**Why This Works**:
- More fraud examples = better pattern recognition
- More legitimate examples = fewer false positives
- Stripe's global network = unique competitive advantage

### Lesson 3: Explanation Matters as Much as Detection

**The Problem**: DNNs are "black boxes" - hard to explain decisions

**The Business Impact**:
- Merchants need to understand why transactions are blocked
- False positives hurt revenue
- Customer support needs explanations
- Compliance requires transparency

**The Solution**: Risk Insights Feature (2020)

**What Merchants See**:
- "This transaction was declined because..."
- "5 cards used from this IP address"
- "Cardholder name doesn't match email"
- "Unusual spending velocity detected"

**Technical Implementation**:
- Feature contribution analysis
- Geographic mapping
- Related transaction context
- Elasticsearch for fast lookups
- Internal debugging tools

## Performance Metrics & Results

### Real-World Performance
- **Decision Time**: < 100 milliseconds (faster than human blink)
- **False Positive Rate**: 0.1% (blocks only 1 in 1000 legitimate transactions)
- **Scale**: Billions of transactions processed
- **Accuracy**: Maintains high fraud detection while minimizing false positives

### Architecture Performance Improvements
- **DNN-Only Migration**: Maintained performance while dramatically improving speed
- **10x Data Scaling**: Significant performance improvements
- **Training Speed**: Much faster with DNN-only architecture
- **Experimentation Velocity**: Engineers can iterate much faster

## System Design Interview Points

### Scalability Challenges
1. **Real-time Processing**: Must decide in < 100ms
2. **Global Scale**: Worldwide payment network
3. **High Throughput**: Billions of transactions
4. **Data Volume**: Massive training datasets (100x scaling experiments)

### Key Trade-offs Made
1. **Model Complexity vs. Interpretability**
   - **Chose**: Complex DNN for better performance
   - **Compensated**: Built extensive explanation tools
   - **Result**: Best of both worlds

2. **Speed vs. Accuracy**
   - **Constraint**: < 100ms decision time
   - **Solution**: Optimized architecture for speed
   - **Result**: Maintained 0.1% false positive rate

3. **Memorization vs. Generalization**
   - **Problem**: XGBoost provided memorization, DNN provided generalization
   - **Solution**: Multi-branch DNN that does both
   - **Result**: Better performance than either alone

### Technical Challenges Solved
1. **Class Imbalance**: Fraud is rare (1/1000) - solved with network effects
2. **Real-time Constraints**: < 100ms decisions - solved with optimized architecture
3. **Scale**: Billions of transactions - solved with DNN-only architecture
4. **Interpretability**: Black box models - solved with risk insights feature

### Architecture Decision Rationale
1. **Why DNN-Only?**
   - Better scalability for billions of transactions
   - Compatible with cutting-edge ML techniques
   - Much faster training and experimentation
   - Better parallelization

2. **Why Multi-Branch?**
   - Increased representational capacity
   - Better memorization + generalization balance
   - Resistant to overfitting
   - Specialized pattern learning

## Business Impact & Real-World Results

### User Experience Improvements
- **Merchants**: Get clear explanations when transactions are blocked
- **Customers**: Only 0.1% of legitimate transactions are blocked
- **Support Teams**: Better debugging tools and explanations
- **Compliance**: Transparent decision-making for regulators

### Technical Benefits
- **Faster Development**: Engineers can experiment much faster
- **Better Scalability**: DNN-only architecture handles billions of transactions
- **Enhanced Monitoring**: Risk insights help understand model behavior
- **Cost Efficiency**: Faster training means lower compute costs

## Future Directions & Innovation

### Planned Improvements
- **100x Data Scaling**: Currently testing with 100x more training data
- **Advanced ML Techniques**: Transfer learning, embeddings, transformer models
- **Post-Payment Analysis**: Detect fraud even after transaction completes
- **Real-time Learning**: Continuous model updates without downtime

### Innovation Areas
- **Network Effects**: Leveraging Stripe's global merchant network
- **Cross-Platform Insights**: Learning from patterns across all merchants
- **Advanced Explainability**: Even better fraud decision explanations
- **Edge Computing**: Bringing fraud detection closer to users

## Interview Questions You Should Be Ready For

### System Design Questions
1. **"Design a real-time fraud detection system with < 100ms latency"**
   - Start with requirements (speed, accuracy, scale)
   - Discuss feature engineering (1,000+ features)
   - Explain architecture choices (DNN vs traditional ML)
   - Address scalability (billions of transactions)
   - Cover monitoring and explainability

2. **"How would you handle model explainability in a black box system?"**
   - Discuss the business need for explanations
   - Explain feature contribution analysis
   - Cover risk insights and user-facing explanations
   - Address internal debugging tools

3. **"What's your approach to A/B testing ML models in production?"**
   - Discuss gradual rollout strategies
   - Cover metrics (false positives, false negatives)
   - Explain how to measure business impact
   - Address rollback procedures

### Technical Questions
1. **"How do you handle class imbalance in fraud detection?"**
   - Explain the 1/1000 fraud rate problem
   - Discuss network effects and data scaling
   - Cover feature engineering for rare events
   - Address evaluation metrics for imbalanced data

2. **"How would you scale ML models to process billions of transactions?"**
   - Discuss architecture choices (DNN vs XGBoost)
   - Cover training scalability and parallelization
   - Explain inference optimization
   - Address data pipeline challenges

3. **"Why might you choose DNN over traditional ML for fraud detection?"**
   - Discuss scalability requirements
   - Cover feature learning capabilities
   - Explain training speed and experimentation
   - Address interpretability trade-offs

### Architecture Questions
1. **"How do you handle model retraining at scale?"**
   - Discuss continuous learning approaches
   - Cover data pipeline for retraining
   - Explain model versioning and deployment
   - Address performance monitoring

2. **"What's the role of feature engineering in modern ML systems?"**
   - Discuss the 1,000+ features in Radar
   - Cover domain expertise vs automated feature learning
   - Explain feature selection and importance
   - Address feature store and management

## Key Takeaways for Interviews

### Technical Insights
1. **Start Simple, Evolve Complex**: Begin with basic models, iterate to advanced architectures
2. **Data is King**: More data often trumps better algorithms (10x, 100x scaling)
3. **Explainability Matters**: Build tools to understand model decisions
4. **Scale Considerations**: Design for real-time, high-throughput systems

### Business Insights
5. **Trade-offs are Everywhere**: Speed vs. accuracy, complexity vs. interpretability
6. **Network Effects**: Leverage platform data for competitive advantage
7. **Continuous Improvement**: Never stop evolving your ML architecture
8. **User Experience**: Technical decisions must serve business needs

### System Design Principles
9. **Performance First**: < 100ms decisions, 0.1% false positive rate
10. **Scalability by Design**: Billions of transactions, global scale
11. **Monitoring & Observability**: Understand what your models are doing
12. **Experimentation Velocity**: Fast iteration enables better models

---

*This case study demonstrates how Stripe evolved their ML system over 7 years, making key architectural decisions, handling scale challenges, and balancing multiple competing requirements. It's a perfect example of real-world ML system design at scale.*
