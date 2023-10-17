# CVE on Kubernetes Report

| Name               | Time       |
| ------------------ | ---------- |
| Kaitao Qiu (Miles) | 2023/10/16 |



[TOC]

## Code Injection via Insecure Yaml.load **CVE-2020-14343**

[Link to Report](https://hackerone.com/reports/1051192)

1. **Report Background:** The reporter identified a security vulnerability in Kubernetes' repository and a tool named 'test-infra.' This vulnerability involves the handling of YAML files, specifically the insecure use of the `yaml.load()` function, which can be exploited for code injection attacks.
2. **Vulnerable Code Locations:** The report specifies the exact code locations, including specific line numbers in the `main.py` and `update_config.py` files, as well as the `get_app_config()` and `main()` functions.
3. **Kubernetes Versions:** The report mentions that this vulnerability affects the latest version of Kubernetes, which is the main branch on GitHub.
4. **Reproduction Steps:** The report provides steps to reproduce the vulnerability, including installing the Gubernator frontend, saving the provided `config.yaml` file as Gubernator's configuration file, and executing the vulnerable code after updating the configuration.
5. **Vulnerability Impact and Exploitation Conditions:** The report notes that the exploitation level of the vulnerability depends on the version of the PyYaml library. For PyYaml >= 5.1, the exploitation conditions are limited and require Gubernator to run in a component that previously imported the `subprocess` module. For PyYaml < 5.1, the vulnerability is always exploitable. The report also mentions the complexity and possibilities of the vulnerability.
6. **Solution:** The report proposes a potential solution, suggesting replacing the insecure `yaml.load()` function with its secure equivalent, `yaml.safe_load()`.
7. **Vulnerability Impact:** The report points out that attackers can execute system commands by constructing malicious YAML files, which may require user interaction.
8. **CVE Assignment:** The report references CVE-2020-14343 and provides a link to a detailed article on how to exploit the vulnerability.
9. **Fix and Reward:** The report later mentions that the issue has been resolved and provides the corresponding fix link. The vulnerability is considered low risk, and the reporter is awarded for the quality of the report and for providing multiple PoCs (Proof of Concepts).

## Node Disk Denial of Service by Writing to Container /etc/hosts **CVE-2020-8556 and 8557**

[Link to Report](https://hackerone.com/reports/867699)

1. **Report Background:** This report, submitted in May 2020, highlights that the `/etc/hosts`, `/etc/hostname`, and `/etc/resolv.conf` files in Pods are not read-only. Ordinary Pods running in a Kubernetes cluster can cause host crashes by writing data to the `/etc/hosts` file. This vulnerability affects not only `/etc/hosts` but also involves `/etc/resolve.conf` and `/etc/hostname`.
2. **Kubernetes Versions:** The vulnerability affects versions 1.18 and below.
3. **Reproduction Steps:** The report provides reproduction steps, including creating a Pod using `kubectl` and then using the `kubectl exec` command to write a large amount of data to the `/etc/hosts` file. This results in a gradual decrease in disk space on the host until it is full.
4. **Vulnerability Impact:** If someone creates a Pod using Kubernetes in a public cloud, the host provided by the cloud provider may crash due to insufficient disk space, leading to a Denial of Service (DoS) attack.
5. **Mitigation Measures:** The report mentions ongoing discussions regarding mitigation measures, including aligning the handling of the `/etc/hosts` file with `/etc/hostname` and `/etc/resolv.conf`, directly writing the file into the container rather than bind mounting. Additionally, a short-term solution is discussed, which involves counting the `/etc/hosts` file towards the Pod's storage quota.
6. **Report Handling:** The report was initially assessed as a medium risk but was later corrected to medium risk. After being validated, the report was submitted for remediation. Subsequently, the report was closed and marked as resolved.
7. **Vulnerability Disclosure:** Eventually, this vulnerability was disclosed, and CVE ID CVE-2020-8557 was assigned. The reporter was awarded $1,000 for their contribution.

## Ingress-nginx Annotation Injection Allows Retrieval of Ingress-nginx Service Account Token and Secrets Across All Namespaces **CVE-2021-25742**

[Link to Report](https://hackerone.com/reports/1378175)

This report involves a security vulnerability in Kubernetes related to Ingress-nginx. Here is a summary of the report:

1. **Report Background:** The report was submitted in October 2021, involving a variant of CVE-2021-25742. The reporter discovered that most template variables in Ingress-nginx's `nginx.tmpl` were not properly escaped, allowing for arbitrary injection of nginx directives. Specifically, the `nginx.ingress.kubernetes.io/connection-proxy-header` annotation was not validated or escaped and was directly inserted into the `nginx.conf` file. An attacker in a multi-tenant cluster with permission to create/modify ingresses could inject content into the `connection-proxy-header` annotation and read arbitrary files from the ingress controller, including the service account.
2. **Vulnerability Impact:** An attacker with permission to create/modify ingresses in one namespace could inject content into the `connection-proxy-header` annotation and read arbitrary files from the ingress controller, including the service account, which has permission to read secrets in all namespaces.
3. **Remediation Measures:** The report pointed out that the root cause of the issue is in the snippet annotation features. It suggested validating and escaping all annotations to prevent injection of nginx directives. The fix should include proper validation and escaping for all annotations.
4. **Report Handling:** The report was initially marked as needing more information, then underwent further investigation. It was later confirmed and submitted to the appropriate remediation team. The report was evaluated as medium risk and received a $2,500 bounty.
5. **Vulnerability Disclosure:** Finally, the vulnerability was disclosed. The reporter requested to be credited as "Anthony Weems."