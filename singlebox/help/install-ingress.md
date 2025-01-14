## Install krew

(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)

Add the $HOME/.krew/bin directory to your PATH environment variable. To do this, update your .bashrc  file and append the following line:

export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"

## Install the plugin ingress-rule

kubectl krew install ingress-rule

kubectl ingress-rule -n $NAMESPACE set $INGRESSNAME --service $SERVICENAME --port 80 --host $HOST

Delete the previously added rule:

kubectl ingress-rule -n $NAMESPACE delete $INGRESSNAME --service $SERVICENAME

