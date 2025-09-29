# 📒 Notes: How We Built It - Stripe Radar

## 1. Context & Metadata

* **Blog Title**: How we built it: Stripe Radar
* **Author**: Ryan Drapeau, Payment Intelligence Engineer at Stripe
* **Date**: March 29, 2023
* **Topic Area**: Fraud detection, Machine Learning, Real-time payment processing
* **Main Purpose**: Explain the evolution and architecture of Stripe's fraud prevention system, sharing key lessons learned over 7 years of development

## 2. Problem Definition

### Core Problem
* **What**: Detect fraudulent payment transactions in real-time during checkout
* **Critical Timing**: Decision must be made in <100ms between "purchase" click and transaction confirmation
* **Scale**: Billions of legitimate payments, fraud rate ~0.1% (1 in 1000 payments)
* **Constraints**:
  - Accuracy: Must be extremely precise (0.1% false positive rate)
  - Speed: <100ms response time
  - Cost: Inexpensive per transaction
  - Balance: Block bad transactions vs. false positives (hurt customers and revenue)

### Why It Matters
- Online payment fraud is a significant business problem
- False positives directly impact user experience and merchant revenue
- Fraud patterns constantly evolving (stolen cards → card testing attacks)

## 3. High-Level Architecture

### Core Components
```
Payment Request → Radar Assessment → Decision (<100ms)
                     ↓
                [1000+ Features] → ML Model → Risk Score
                     ↓
              [Block/Allow/Review] → Transaction Processing
```

### Key Design Pattern
- **Real-time Feature Engineering**: Extract 1000+ transaction characteristics
- **Multi-branch DNN Architecture**: Inspired by ResNeXt for memorization + generalization
- **Network Effect**: Leverage Stripe's global payment network for context

## 4. Key Techniques / Solutions

### ML Architecture Evolution
* **Started**: Simple logistic regression
* **Middle**: Ensemble "Wide & Deep" model (XGBoost + DNN)
* **Current**: Pure DNN-only architecture with multi-branch design

### Multi-Branch DNN Architecture
* **Inspiration**: ResNeXt "Network-in-Neuron" strategy
* **Design**: Split computation into distinct branches (threads)
* **Benefits**:
  - Better memorization (replaces XGBoost functionality)
  - Enhanced generalization capabilities
  - Improved training speed and scalability
  - Compatible with advanced ML techniques (transfer learning, embeddings)

### Data Strategy
* **Training Data Scaling**: 10x increase in training data → significant improvements
* **Experimentation**: Currently testing 100x version
* **Network Effect**: Leverage breadth of Stripe network for feature engineering

### Feature Engineering
* **1000+ Characteristics** assessed per transaction
* **Examples**:
  - Payment velocity patterns
  - Cardholder name vs. email matching
  - Number of cards per IP address
  - Geographic location correlations
  - Historical transaction patterns

## 5. Design Patterns / Principles

### Architectural Patterns
* **"Don't get comfortable with ML architecture"** - Continuous evolution as network grows
* **"Memorization + Generalization"** - Balance between pattern recognition and feature learning
* **"Network Effect Leverage"** - Use platform scale as competitive advantage
* **"Explanation as Product Feature"** - Transparency builds trust and enables optimization

### Technical Principles
* **Parallelizable Training**: DNN architecture enables faster experimentation cycles
* **Feature Context Awareness**: Model evaluates features in correlation, not isolation
* **Real-time Decision Making**: Optimize for sub-100ms response times
* **Continuous Learning**: Architecture must adapt to evolving fraud patterns

## 6. Best Practices & Pitfalls

### ✅ Best Practices
* **Continuous Architecture Evolution**: Don't stick with outdated ML approaches
* **Balance Memorization vs. Generalization**: Both are needed for fraud detection
* **Invest in Explainability**: Users need to understand decisions
* **Leverage Network Effects**: Use platform scale for competitive advantage
* **Optimize for Training Speed**: Faster iteration enables better experimentation

### ⚠️ Pitfalls / Common Mistakes
* **Overfitting Risk**: Increasing model depth too much can memorize noise
* **Black Box Problem**: DNNs are harder to explain than simpler models
* **Performance Regression**: Removing components (like XGBoost) can cause 1.5% recall drop
* **Ignoring User Experience**: False positives hurt both customers and merchants
* **Static Architecture**: Not evolving with network growth and ML advances

## 7. Metrics & Evaluation

### System Metrics
* **Latency**: <100ms decision time (critical constraint)
* **Throughput**: Billions of transactions processed
* **Training Speed**: Faster iteration cycles with DNN-only architecture

### ML Metrics
* **False Positive Rate**: 0.1% (extremely low)
* **Recall**: Maintained performance through architecture transitions
* **Model Performance**: Significant improvements with each architectural evolution

### Business Metrics
* **Fraud Detection Accuracy**: Balance between blocking bad vs. allowing good transactions
* **User Satisfaction**: False positives impact customer experience
* **Merchant Revenue**: Incorrect blocks hurt business bottom line

## 8. Case Study / Example

### Real-World Application
Stripe Radar processes billions of payments globally, making real-time fraud decisions that directly impact merchant revenue and customer experience. The system evolved from simple models to sophisticated multi-branch DNNs over 7 years, demonstrating the importance of continuous architectural evolution in production ML systems.

### Risk Insights Feature (2020)
- Shows users which transaction features contributed to decline decisions
- Includes visual maps of purchase/shipping locations
- Uses Elasticsearch for related transaction lookup
- Helps merchants understand and optimize their data quality

## 9. Reflection & Key Learnings

### What I Learned
* **Architecture Evolution is Critical**: Don't get attached to specific ML techniques; evolve with the network and technology
* **Explainability is Product Feature**: Users need to understand ML decisions, not just accept them
* **Network Effects in ML**: Platform scale creates competitive advantages through better data and features
* **Speed Enables Experimentation**: Faster training cycles unlock more innovation
* **Balance is Everything**: Trade-offs between accuracy, speed, explainability, and cost are constant

### Connections to Other Concepts
* **Two-Phase Systems**: Similar to recommendation systems with candidate generation + ranking
* **Feature Stores**: Need for consistent feature engineering across training/serving
* **Real-time ML**: Similar challenges to ad serving and content recommendation
* **Model Monitoring**: Continuous evolution requires drift detection and performance tracking

### Reusable Ideas
* **Multi-branch DNN Architecture**: Applicable to other domains requiring memorization + generalization
* **Network Effect Leverage**: Use platform scale for ML competitive advantage
* **Explainability Investment**: Build transparency into ML products from the start
* **Continuous Architecture Evolution**: Plan for ML system evolution, not just initial deployment

## 10. Cross-Links & Tags

### Tags
- `#FraudDetection` `#RealTimeML` `#DNNArchitecture` `#PaymentProcessing`
- `#ExplainableAI` `#NetworkEffects` `#MLSystemEvolution` `#FeatureEngineering`

### Related Topics
- Real-time ML serving systems
- Multi-stage ML architectures
- Explainable AI in production
- Payment processing systems
- ML system evolution patterns

---

**Source**: [How we built it: Stripe Radar](https://stripe.com/blog/how-we-built-it-stripe-radar) - March 29, 2023
