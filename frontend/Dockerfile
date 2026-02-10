FROM nginx:1.25-alpine

RUN rm /etc/nginx/conf.d/default.conf

COPY nginx.conf /etc/nginx/conf.d/default.conf

COPY index.html /usr/share/nginx/html/index.html

#When running in K8S, the API_BASE is handled by ginx proxy
#For local dev, we inject it via script
RUN sed -i "s|window.__API_BASE__ || 'http://localhost:8080'|window.__API_BASE__ || ''|" \
    /usr/share/nginx/html/index.html

EXPOSE 80

HEALTHCHECK --interval=15s --timeout=3s --retries=3 \
  CMD wget -qO- http://localhost:80/ || exit 1
