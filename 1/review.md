> The comment is organized by two parts, namely Summary of the paper and my personal opinions.

## Summary:
This paper presents a new operating system named Singularity in order to explore how advances in languages, tools, and operating systems might produce a more robust and dependable software platform. Singularity incorporates three key architectural features, namely software-isolated processes (SIP), contract-based channels and manifest-based programs (MBP). The background behind is that existing operating systems expose increasingly obvious shortcomings, which promotes them to propose an experimental system for exploring implementations and trade-offs. 

The primary characteristic for SIP is tremendous autonomy, and that is to say that each SIP has its own data layouts, run-time system, and garbage collector. The process isolation of SIP is provided by software, which has lower overhead compared to hardware protected processes. All communication between SIPs are realized through contract-based channels, which is a bi-directional in-order message queue with two endpoints. Communication across a channel is described by a channel contract, consisting of message declarations and a set of named protocol states. Besides, a contract verifier is utilized to detect and prevent mistakes. The last part is Manifest-Based Program (MBP), meaning a program defined by a static manifest that describes an MBP's code resources and depicts a machine-checkable, declarative expression of the MBP's expected behavior.

The Singularity kernel, microkernel architecture, only provides an environment with threads, memory, schedule and access to other MBPs via channels. Summary of the functional parts is followed. In accordance with the principle of least privilege, ABI provides an identification mechanism for accessing primitive and process-local operations. Moreover, the state isolation invariant is maintained for preventing illegal state alteration. Memory management in Singularity follows the memory independence invariant, which means that pointer of SIP must point to its own memory or to memory owned by the SIP in the exchange heap. In this way, all data passed between SIPs must reside in the exchange heap. And thanks to the principle of memory independence invariant, each SIP has its own garbage collector. The Singularity kernel and SIPs are multi-threaded and linked stack struct is employed to reduce thread memory overhead. 

From the perspective of the OS design space, a new Compile-Time Reflection (CTR) is developed. CTR contain a high-level construct named transform, which allows programmers to write inspection and generation code in a pattern matching and template style. Besides, protection domains, composed of a kernel domain and non-kernel domains, is designed to provide an additional level of hardware-based protection around SIPs. In Singularity, proof-carrying code and typed assembly language (TAL) are combined with the Bartok compiler to translate an MBP’s MSIL code to native machine language code.

Form Singularity project, they draw a significant conclusion that high performance and compatibility can be abandoned when pursuing a more reliable system.

## Thinking:
In my opinion, the biggest highlight of the project Singularity is the low-coupling of each part, which can be reflected by the lightweight Software Isolated Processes (SIP), memory independence invariant, private garbage collector as well as the independent runtime environment. And all the data exchange and communication between SIPs are realized through the contract-based channels and exchange heap. By doing this, each SIP can be regarded as a relatively isolated unit, which has the ability of self-management and can effectively prevent affecting by other illegal operations performed by other SIPs. Although this OS does not take the practicability and compatibility into account, the innovate concepts and ideas of designing a simpler and more reliable system are significantly meaningful.
