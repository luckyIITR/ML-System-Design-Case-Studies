# Stripe Radar: ML System Design Case Study

## Overview
**Source**: [How we built it: Stripe Radar](https://stripe.com/blog/how-we-built-it-stripe-radar)  
**Company**: Stripe  
**Product**: Radar - Fraud prevention solution  
**Timeline**: ~7 years of development  

## Problem Statement

### Core Challenge
- **Real-time fraud detection** in online payments
- **Decision time**: < 100 milliseconds
- **Accuracy requirement**: 0.1% false positive rate
- **Scale**: Billions of transactions
- **Fraud rate**: ~1 in 1,000 payments (rare event problem)

### Key Constraints
1. **Speed**: Must make decisions in < 100ms
2. **Accuracy**: Balance between blocking fraud vs. false positives
3. **Cost**: Must be inexpensive per transaction
4. **Rarity**: Fraud is rare (1/1000), making it a class imbalance problem

## System Architecture Evolution

### Initial Architecture (Early Years)
- **Simple ML models**: Logistic regression
- **Approach**: Basic feature engineering + traditional ML

### Intermediate Architecture (Wide & Deep Model)
- **Components**:
  - **Wide part**: XGBoost (memorization)
  - **Deep part**: Deep Neural Network (generalization)
- **Benefits**: Combined memorization + generalization
- **Limitations**: 
  - XGBoost not parallelizable at scale
  - Incompatible with advanced ML techniques
  - Slow retraining cycles
  - Limited experimentation velocity

### Current Architecture (DNN-Only)
- **Migration**: Mid-2022
- **Architecture**: Pure Deep Neural Network
- **Inspiration**: ResNeXt "Network-in-Neuron" strategy
- **Key Innovation**: Multi-branch DNN architecture

## Technical Deep Dive

### Multi-Branch DNN Architecture
```
Input Features → Multiple Branches → Summation → Output
```

**Design Principles**:
- **Branches**: Distinct computation threads
- **Each branch**: Small network within the larger network
- **Output**: Sum of all branch outputs
- **Benefits**: 
  - Increased representational capacity
  - Better memorization + generalization
  - Resistant to overfitting

### Feature Engineering
- **Feature count**: 1,000+ characteristics per transaction
- **Examples**:
  - Cardholder name vs. email matching
  - Number of cards per IP address
  - Payment velocity
  - Unusual volume patterns
  - Geographic correlations

### Training Data Strategy
- **Current**: 10x increase in training data
- **Experiments**: 100x data increase planned
- **Key insight**: More data = significant performance improvements
- **Challenge**: Training time scales linearly with data size

## Key Lessons Learned

### Lesson 1: Don't Get Comfortable with ML Architecture
**Evolution Path**:
```
Simple Models → Wide & Deep → DNN-Only
```

**Trade-offs**:
- **XGBoost removal**: Would cause 1.5% recall drop
- **Solution**: Enhanced DNN to replace XGBoost memorization
- **Result**: Faster training, better scalability, more adaptable

**Key Insight**: Architectural evolution drives performance leaps

### Lesson 2: Scale Training Data Strategically
**Approach**:
- Start with simple architecture
- Scale data exponentially
- Measure performance improvements

**Results**:
- 10x data increase → significant improvements
- 100x increase in progress
- Training speed improvements enabled larger datasets

### Lesson 3: Explanation Matters as Much as Detection
**Challenge**: DNNs are black boxes
**Solution**: Risk insights feature (2020)

**Features**:
- Transaction decline explanations
- Feature contribution analysis
- Geographic mapping
- Related transaction context
- Elasticsearch integration for quick lookups

**Internal Tools**:
- Feature contribution tables
- Debug support cases
- Model interpretability

## Performance Metrics

### Accuracy
- **False positive rate**: 0.1%
- **Decision time**: < 100ms
- **Scale**: Billions of transactions

### Model Performance
- **Architecture migration**: Maintained performance while improving speed
- **Data scaling**: 10x data → significant improvements
- **Training speed**: Improved with DNN-only architecture

## System Design Interview Points

### Scalability Considerations
1. **Real-time processing**: < 100ms response time
2. **High throughput**: Billions of transactions
3. **Global scale**: Worldwide payment network
4. **Data volume**: Massive training datasets

### Trade-offs Made
1. **Model complexity vs. interpretability**
   - Chose DNN over simpler models
   - Built explanation tools to compensate
2. **Speed vs. accuracy**
   - Optimized for < 100ms decisions
   - Maintained 0.1% false positive rate
3. **Memorization vs. generalization**
   - Multi-branch architecture balances both

### Technical Challenges
1. **Class imbalance**: Fraud is rare (1/1000)
2. **Real-time constraints**: < 100ms decisions
3. **Scale**: Billions of transactions
4. **Interpretability**: Black box ML models

### Architecture Decisions
1. **Why DNN-only?**
   - Better scalability
   - Compatible with advanced ML techniques
   - Faster training and experimentation
2. **Why multi-branch?**
   - Increased representational capacity
   - Better memorization + generalization
   - Overfitting resistance

## Business Impact

### User Experience
- **Merchants**: Clear fraud decision explanations
- **Customers**: Minimal false positives (0.1%)
- **Support**: Better debugging tools

### Technical Benefits
- **Faster iteration**: Improved experimentation velocity
- **Better scalability**: DNN-only architecture
- **Enhanced monitoring**: Risk insights and explanations

## Future Directions

### Planned Improvements
- **100x data scaling**: Further performance gains
- **Advanced techniques**: Transfer learning, embeddings
- **Post-payment analysis**: Fraud detection after transaction

### Innovation Areas
- **Network effects**: Leveraging Stripe's global network
- **Real-time learning**: Continuous model updates
- **Cross-platform insights**: Multi-merchant patterns

## Interview Questions You Should Be Ready For

### Technical Questions
1. "How would you design a fraud detection system for a payment processor?"
2. "What are the trade-offs between model complexity and interpretability?"
3. "How do you handle class imbalance in fraud detection?"
4. "How would you scale ML models to process billions of transactions?"

### System Design Questions
1. "Design a real-time fraud detection system with < 100ms latency"
2. "How would you handle model explainability in a black box system?"
3. "What's your approach to A/B testing ML models in production?"
4. "How do you balance false positives vs. false negatives in fraud detection?"

### Architecture Questions
1. "Why might you choose DNN over traditional ML for fraud detection?"
2. "How do you handle model retraining at scale?"
3. "What's the role of feature engineering in modern ML systems?"
4. "How do you ensure model performance doesn't degrade over time?"

## Key Takeaways for Interviews

1. **Start simple, evolve complex**: Begin with basic models, iterate to advanced architectures
2. **Data is king**: More data often trumps better algorithms
3. **Explainability matters**: Build tools to understand model decisions
4. **Scale considerations**: Design for real-time, high-throughput systems
5. **Trade-offs are everywhere**: Speed vs. accuracy, complexity vs. interpretability
6. **Network effects**: Leverage platform data for competitive advantage
7. **Continuous improvement**: Never stop evolving your ML architecture

---

*This case study demonstrates how a major tech company evolved their ML system over 7 years, making key architectural decisions, handling scale challenges, and balancing multiple competing requirements.*
