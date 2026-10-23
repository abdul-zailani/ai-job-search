# Panduan Melamar & Jawaban Wawancara Teknis (SRE & DevOps)

Dokumen ini disiapkan untuk melamar ke posisi:
1. **BTPN Syariah** (DevOps Engineer) - [Job Link](https://www.linkedin.com/jobs/view/4475549573)
2. **AlloFresh** (Site Reliability Engineer) - [Job Link](https://www.linkedin.com/jobs/view/4474141689)

---

## 1. Strategi Elevate Pitch (Perkenalan Diri 2 Menit)
> *"Nama saya Abdul Aziz Zailani, Senior SRE dan DevOps Engineer dengan pengalaman lebih dari 6 tahun dalam pengelolaan multi-cloud (AWS & GCP) serta orkestrasi Kubernetes. Saat ini di Lion Parcel, saya memimpin inisiatif migrasi core microservices dan database dari GCP ke AWS EKS dengan 0% downtime, serta merancang auto-scaling runner fleet dengan GitHub Actions Runner Controller (ARC) dan standardisasi CI/CD berbasis Jenkins Job DSL/JCasC. Fokus utama saya adalah operational excellence, mengeliminasi toil melalui otomatisasi IaC dan GitOps, serta mempercepat MTTR insiden produksi melalui observabilitas terdistribusi (Grafana Tempo dan Prometheus)."*

---

## 2. Bank Soal & Jawaban Teknis Sering Ditanyakan

### Q1: Bagaimana Anda merancang migrasi multi-cloud tanpa downtime (0% downtime)?
- **Strategi Data (DB):** Gunakan replikasi sinkron/asinkron antar cloud (misal logical replication / DMS) dengan validasi checksum lag sebelum cutover.
- **Strategi Traffic (Network):** Gunakan DNS weighted routing (Route53) atau canary traffic shift di ingress ALB/Gateway. Pindahkan beban bertahap (5% -> 25% -> 50% -> 100%) sambil pantau error rate & p99 latency di Grafana.
- **Rollback Plan:** Pertahankan database target mereplikasi balik ke database asal (reverse replication) selama masa grace period 24-48 jam.

### Q2: Mengapa memilih GitOps dengan ArgoCD dan bagaimana menangani drift?
- **SSOT (Single Source of Truth):** Git adalah representasi tunggal dari desired state cluster.
- **Automated Sync & Self-Healing:** ArgoCD secara kontinyu membandingkan desired state di repository Git dengan live state di Kubernetes. Ketika ada mutasi manual (drift), ArgoCD otomatis mendeteksi dan mengembalikan (reconcile) konfigurasi sesuai Git.
- **Audit & Governance:** Setiap perubahan infrastruktur wajib melalui Pull Request dan review, mempermudah compliance audit di industri perbankan/finansial syariah.

### Q3: Bagaimana Anda mengukur dan menjaga keandalan sistem produksi (SLI/SLO/Error Budget)?
- **SLI (Service Level Indicator):** Metrik kuantitatif yang mengukur pengalaman pengguna nyata, contohnya availability (rasio request HTTP sukses non-5xx) dan latency (persentil p95/p99 respons API).
- **SLO (Service Level Objective):** Target keandalan yang disepakati (misal 99.9% request sukses per bulan).
- **Error Budget:** Batas toleransi kegagalan (0.1%). Jika budget habis, freeze fitur baru dan alokasikan sprint khusus untuk reliability and bugfix.
