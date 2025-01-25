# CrossLang  
**A Practical AI Benchmark for Cross-Language Code Translation**  

---
## **Overview**  
This AI benchmark evaluates the ability to transcribe GitHub repositories into other languages. It aims to have real-world impact for developers by enabling them to use any package in their preferred programming language. 

This targets the weakness of LLMs since the goal is to take something memorized (i.e a popular repository in a popular language) and transcribe it into a space that’s sparsely trained on (i.e. an unpopular language — like most languages).

Like spoken languages, transcribing software is fairly common for humans.

Unlike spoken languages, transcribing software requires reasoning to handle cases like:
1. Outdated, platform, or incompatible dependencies, idiosyncrasies, and features. 
2. Translating language specific idiosyncrasies.
3. Translating between paradigms like in pure OOP, functional, and other languages. 

---

## **Core Process**  

### **Step-by-Step Workflow**  

**Repeat until maximum prompt cost is met**
1. **Find a GitHub repository with test coverage**  
   1. initialize repository, stars, tests, prompt
2. **Translate to Target Languages**:  
   - For each target language:  
     - Transcribe the repository into the target language.  
     - Run the translated tests.  
       - Increment score by `Repo Stars × % Test Coverage` if all the tests pass.
3. **Back-Translate to Original Language**:  
   - For both successful and failed translations
     - Transcribe the modified code back to the original language.  
     - Run the original repository’s tests on the back-translated code.  
       - Increment score by `Repo Stars × % Test Coverage` if all the tests pass.
4. **Publish to leaderboard landing page**
   1. Date, Company, LLM, Score, $/Score.

---
## **Scoring System**  
### **Formula**  
Score += Repo Stars × % Test Coverage × Transcription Success (Front or Back)

### **Key Points**  
**Popular repositories are priortized**. Popular codebases are mostly more desired in other languages, complete, and have better testing.

**Back-Translation** 
It’s likely that majority of the score will be from translating back to the original language due to some translations would not be possible do to unavoidable aspects of the target language. This rewards: 
1. A computable proxy for human readability. Since an AI must be able to read and work with its own generations.
2. An AI’s ability to work with broken and incomplete code. Since even failed transcriptions must be attempted to go back into original code. 

---

## **Key Features**  
### 1. **Anti-Gaming Design**  
- **Live Repositories**: Pulls from GitHub on every run which avoids static datasets.  
- **Large evaluation space:** +500 Languages * many repositories * 2 translation directions
- **Full-Stack Validation**: Requires end-to-end functionality (code + tests) in both directions.  

### 2. **Real-World Reasoning**  
- Replace incompatible dependencies (e.g., Python’s `subprocess` → JavaScript’s `child_process`).  
- Handle language-specific quirks (e.g., Python indentation → Rust’s `{}` syntax).  
- Debug failed tests during back-translation.  

### 3. Helps AI
- Back translating to orignal code is a trainable feature to improve existing LLMs.
- Unbounded scoring allows easily comparing LLMs over the years.

### 4. Helps developers
* Developers can use **any library in their preferred language** (e.g., Python’s NumPy in Rust).  

---

## Example Workflow (Sequential)
| Step | Action                                         | Outcome                                                      | Total Score |
|------|------------------------------------------------|--------------------------------------------------------------|-------------|
| 1    | Translate `Python/requests` → `Rust`           | ✅ Rust tests pass → **+9,000 points** (Repo: 10k★, 90% coverage) | **9000**    |
| 2    | Back-translate `Rust/requests` → `Python`      | ✅ Original Python tests pass → **+9,000 points**             | **18000**   |
| 3    | Translate `Python/requests` → `Julia/requests` | ❌ Some Julia tests fail → **0 points**                       | **18000**   |
| 4    | Back-translate `Julia/requests` → `Python`     | ✅ Original Python tests pass → **+9,000 points**             | **27000**   |


---

## **Implementation Challenges**  
| Challenge | Example |  
|-----------|---------|  
| **Dependency Hell** | Translating Python’s PyTorch (C++ bindings) to pure JavaScript. |  
| **Test Ambiguity** | Tests relying on language-specific behavior (e.g., Ruby’s `nil` vs. JavaScript’s `undefined`). |  
| **Performance Gaps** | Translating Python to C++ may inadvertently introduce memory leaks. |  

---

## **Future Roadmap**  
- **Auto-PR Translated Code**: Submit translations as `{AI Vendor}/Translated/{Lang}/{Repo}` via GitHub PRs.  
  - Port to respective package managers?
  - Partial updates?
- **Test Runtime Tracking**: Compare execution times of original vs. translated tests (e.g., Rust ↔ Python speed tradeoffs).  
- **Language runtime benchmarking**

---

## **Comparison to Other Benchmarks**  
| Feature        | CrossLang                | Traditional Benchmarks           |
|----------------|--------------------------|----------------------------------|
| **Dataset**    | Live GitHub repos        | Static synthetic problems        |
| **Evaluation** | Round-trip test passes   | Single-direction code completion |
| **Scope**      | Entire repositories      | Snippets or single files         |
| **Longevity**  | Score grows indefinitely | Fixed maximum score              |
